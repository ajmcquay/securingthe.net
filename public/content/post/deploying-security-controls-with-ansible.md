+++
author = "AJ McQuay"
categories = ["Ansible", "Automation", "Tools"]
date = 2018-03-09T18:04:36Z
description = ""
draft = false
image = "/images/2018/03/ansible_logo_round.png"
slug = "deploying-security-controls-with-ansible"
tags = ["Ansible", "Automation", "Tools"]
title = "Deploying Security Controls with Ansible"

+++

I use several tools for various security or compliance controls on production Linux servers. Some of those aren't automatically installed/maintained through our package management utility, and with 100+ machines it is far too time-consuming to manually deal with each one. I've started experimenting with Ansible to simplify this and thought I'd share the first role I've written while I'm learning.

## Identify Tools and Required Tasks
The first step was to identify what tools I wanted the playbook to manage, and what steps I take to install and configure them. Two tools I use that aren't managed by Spacewalk are Samhain and M/Monit.
### Samhain
We currently deploy Samhain in a simple standalone fashion that logs locally, then those logs get picked up by our SIEM. I download and compile Samhain from source but don't use a ton of options at compile time as we aren't currently running a Yule server. That means the required tasks are pretty simple:

* Download Samhain
* Extract & verify PGP signature
* Configure, compile, install
* Copy in preferred config
* Initialize or re-initialize file signature database
* Start the daemon

I opted to split these steps into two tasks named install and update.
### M/Monit
M/Monit is just a centralized dashboard used to manage multiple instances of the Linux utility monit. Setting this up just requires customizing the monit configuration file with M/Monit server details and any custom services you want to manage. The tasks here include:

* Make sure monit is installed
* Backup current/default monitrc
* Install new monitrc from template
* Start or restart the service
## Create Ansible role structure
Now it's time to create the folder hierarchy for our Ansible job. When using roles, Ansible expects certain files to exist in specific directories and will automatically pull in your variable files, handlers, templates, etc. If you're unfamiliar, read up on roles here.

Here's my file/folder structure:
```nix
ansible/security-tools/
├── ansible.cfg
├── inventories
│   └── production
│       └── hosts
├── main.yml
└── roles
    ├── monit
    │   ├── files
    │   ├── handlers
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   │   └── monitrc.j2
    │   └── vars
    │       └── main.yml
    └── samhain
        ├── files
        │   └── samhainrc
        ├── handlers
        ├── tasks
        │   ├── install.yml
        │   ├── main.yml
        │   └── update.yml
        ├── templates
        └── vars
            └── main.yml
```
The primary playbook is executed by running the command ansible-playbook main.yml. Here's what it looks like:
```yaml
---
- hosts: all
  become: yes
  roles:
    - {name: samhain, tags: samhain}
    - {name: monit, tags: monit}
```
Pretty simple, right? I added tags so that I can pick and choose which roles I want to run as this project expands. For example I can run just the monit role with the command `ansible-playbook main.yml --tags monit`.
## Roles in Detail
### Monit
Let's take a closer look at each role now, starting with Monit. Here's the roles/monit/tasks/main.yml task file:
```yaml
---
 - stat: path={{ monitrc_path }}
   register: rc

 - name: install monit on CentOS hosts
   yum:
     name: monit
     state: present
   when: ansible_distribution == "CentOS" and ansible_distribution_major_version > '5'

 - name: install monit on Ubuntu hosts
   apt:
     name: monit
     state: present
   when: ansible_distribution != "CentOS"

 - name: backup current monitrc file
   command: mv {{ monitrc_path }} /etc/monitrc.bak
   when: rc.stat.exists

 - name: write new monitrc file and restart the service
   template:
     src: monitrc.j2
     dest: "{{ monitrc_path }}"
     owner: root
     mode: 0600
   notify:
     - stop monit service
     - start monit service
``` 
You can see it referencing the variable `{{ monitrc_path }}`, template file `monitrc.j2`, and handlers `stop monit service` and `start monit service`. Ansible automatically pulls these in from `roles/monit/vars/main.yml`, `roles/monit/templates/`, and `roles/monit/handlers/main.yml`, respectively.

The vars file contains a single line: `monitrc_path: /etc/monitrc`

I won't attach the entire monitrc template, but it just substitutes a single variable from the facts Ansible gathers on each host when first running a playbook. Example:
```nix
set httpd port 2812 and
    use address {{ ansible_default_ipv4.address }}
```
Finally, `roles/monit/handlers/main.yml` contains:
```yaml
---
- name: stop monit service
  service:
    name: monit
    state: stopped
- name: start monit service
  command: monit -c /etc/monitrc
```
### Samhain
Now for a quicker look at the Samhain role. Here are the contents of roles/samhain/tasks/main.yml:
```yaml
---
- include: install.yml
- include: update.yml
```
This simply calls the separate install and update tasks. I typically comment one of them out and just run install if it's a new system needing security tools installed the first time, or update if it's an existing system that just needs an update to the samhain config file.

Install task:
```yaml
---
- stat: path={{ samhain_database }}
  register: sam_db
- stat: path={{ samhainrc_path }}
  register: sam_path
- stat: path={{ samhain_process }}
  register: samhain_pid
- name: Download Samhain package
  get_url:
    url: http://www.la-samhna.de/samhain/samhain-current.tar.gz
    dest: /opt
    checksum: <insert checksum for current version of Samhain>
- name: Unpack Samhain tarball
  unarchive:
    src: /opt/samhain-current.tar.gz
    dest: /opt/
    remote_src: True
- name: Import Samhain development key
  command: gpg --keyserver pgp.key-server.io --recv-key 0F571F6C
- name: Check key fingerprint
  command: gpg --fingerprint 0F571F6C
- name: Verify PGP signature
  shell: gpg --verify /opt/samhain-{{ samhain_version }}.tar.gz.asc /opt/samhain-{{ samhain_version }}.tar.gz
  register: gpg_check
- debug: msg={{gpg_check}}
- name: Unzip second-stage tar file
  unarchive:
    src: /opt/samhain-{{ samhain_version }}.tar.gz
    dest: /opt
    remote_src: True
- name: Install Samhain
  shell: cd /opt/samhain-{{ samhain_version }} && ./configure && make && make install
  args:
    creates: /etc/samhainrc
```
Update task:
```yaml
---
- stat: path={{ samhain_database }}
  register: db
- stat: path={{ samhainrc_path }}
  register: rc
- stat: path={{ samhain_process }}
  register: pid
- name: Stop samhain process
  command: /usr/local/sbin/samhain stop
  when: pid.stat.exists
- name: Copy samhainrc file to client
  copy: src=samhainrc dest=/tmp/samhainrc
    owner=root group=root mode=0644
- name: Backup current samhainrc
  command: mv {{ samhainrc_path }} /etc/samhainrc.bak
  when: rc.stat.exists
- name: Move samhainrc to /etc
  command: mv /tmp/samhainrc {{ samhainrc_path }}
- name: Backup existing samhain database
  command: mv {{ samhain_database }} /var/lib/samhain/samhain_file.bak
  when: db.stat.exists
- name: Update samhain database
  command: /usr/local/sbin/samhain -t init
- name: Start samhain
  command: /usr/local/sbin/samhain -D -t check
```
The file referenced in the copy operation is located at roles/samhain/files/samhainrc.

Vars file:
```yaml
    samhain_database: /var/lib/samhain/samhain_file
    samhainrc_path: /etc/samhainrc
    samhain_process: /var/run/samhain.pid
    samhain_version: <insert current Samhain version>
```   
   
Conclusion

This is all fairly simple but has greatly reduced the amount of time I spend installing and maintaining these tools. I realize the Samhain role doesn't adhere to Ansible's idea of idempotence very well due to the heavy usage of the command module. I'm looking for ways to improve it, but it does what I need with some basic error handling for now.

Hopefully this helps illustrate how Ansible roles work and some of the advantages roles have versus doing everything from one giant playbook. I'm relatively new to Ansible so feel free to leave a reply below or reach out on Twitter if you have questions, comments, or even suggestions on how this could be improved.

