# PWK: Weeks 9-11


My OSCP exam is in 72 hours.  If all goes well, next week could be the end of this 12-week journey.  I wanted to go ahead and post a write-up of the last few weeks. My goal when I started the lab was to complete 30 lab machines and I hit that mark in week 11.

{{< figure src="/content/images/2019/06/Screen-Shot-2019-06-07-at-2.20.45-PM.png" >}}

### Day 59

* Completed host #24.  Here I learned not to trust pre-compiled exploit binaries from GitHub.  I also had some issues with smbserver.py and had to mount a shared folder between my Kali VM and the Offsec-provided Windows 7 VM. Have multiple paths to transfer files between machines at your disposal!

### Day 60

* Recon and enumeration against _Pain_.  Got a low privilege shell and started working on privesc.

### Day 61

* Completed _Pain_. That's 3 of the big 4 down, and 25 hosts toward my goal of 30. Primary lesson from this box is that details matter when preparing an exploit. Some bugs need the right environment to thrive. Also started initial discovery of box #26.

### Day 62

* Completed box 26.  Pretty straightforward, I was briefly concerned that the host might not have been in the right state when I started. I just got lucky and chased the right rabbit pretty quickly.  Also started enumeration of host 27.

### Day 63

* Did some initial recon against two targets that apparently have major dependencies on other systems.  Decided to switch to another box for target #27, then completed it quickly. I also spent some time cleaning up my documentation a bit.

{{< figure src="/content/images/2019/06/Screen-Shot-2019-06-07-at-2.22.20-PM.png" >}}

### Day 66

* Completed target 27. This one taught me to be more thorough in my enumeration (hint, don't just scan TCP ports). Started recon and enumeration of target 28.

### Day 67

* Completed #29 in about 15 minutes. Started enumeration against _Humble,_ which I ceremoniously chose as my 30th target.

### Day 69

* Attempted (and failed) exploitation against _Humble._ Still haven't obtained a low-privilege shell yet. I need more webapp testing experience.

### Day 70

* Finally got a low-priv shell on _Humble_. Started enumerating for privesc.

{{< figure src="/content/images/2019/06/Screen-Shot-2019-06-07-at-2.23.57-PM.png" >}}

### Day 71

* Identified the path to escalate privileges on _Humble_ and what appears to be the exploit I'll need to use. Did some testing but more is needed.

### Day 72

* Finally completed _Humble_ and reached my goal of 30 compromised lab machines. I learned a lot here about being detail-oriented with exploit code, and researching how applications I'm unfamiliar with work and are configured to fit all the puzzle pieces together.

### Day 73

* Started working on my lab & exercise report.

### Day 74

* Finished the lab & exercise report.

### Day 75

* I started going through buffer overflow exercises again and making some notes around workflow

### Day 76

* I started and completed host 31 to unlock the Dev subnet. I realized I hadn't unlocked it yet and just wanted to check that box.

Through week 11 I have invested 130.5 hours into the course and labs. As for this past week (week 12) I have mostly focused on going through buffer overflow resources and compiling a reference sheet for things I want to remember during the exam without having to think about them. Command shortcuts, methods to consider in certain situations, etc.

I also plan to write down some 'exam principles' I'd like to stick to, and to set up a series of alarms on my phone to remind myself to take breaks, eat, and step away during the exam. Wish me luck, hopefully by this time next week I'll officially be an OSCP!

_Photo by [Marvin Ronsdorf](https://unsplash.com/@marvin_ronsdorf?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/finish?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_

