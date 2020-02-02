# My OSCP experience


This morning, 7 days after submitting my exam and lab reports, I received an email from Offensive Security letting me know I had successfully completed the OSCP exam.  I wanted to close out this series of posts by providing a recap of my exam experience.  Sorry - no exam spoilers - just a look at how I handled the exam and some thoughts looking back over it and my time in the course/lab.

# Exam - Day 1

My exam started at 9AM on a Monday. Per the instructions from OffSec I connected to the webcam and ScreenConnect sessions 15 minutes early to connect with my proctor and verify my identity. I got my exam information email right at 9, connected the VPN, ran a couple of tests with the proctor, and was on my way.

The first thing I did was connect to my exam control panel and read through all the instructions for each machine. I used OneNote to document everything related to the course so far, so I'd already prepared a section for the exam. I had a template set up for tracking my activities per-host (which I highly recommend). It was simple but effective, here's a screenshot:

{{< figure src="/content/images/2019/06/Screen-Shot-2019-06-20-at-8.59.13-AM.png" >}}

I had already decided to tackle the buffer overflow first, but before starting that I kicked off recon scans against the other four target machines. Once they were running, I focused on the buffer overflow and finished it within the first hour. I took a 10-15 minute break, got some coffee, and reviewed my initial scan results to choose my next target.

I chose one of the 20-point machines and got a root shell within a couple of hours, so I took a quick lunch break.  At that point I estimated my score to be around 50 points (including 5 bonus points from my lab & exercise report). I was confident I had identified the vulnerability on the 10-point host and the initial exploitation path on the other 20-point host. I had no ideas yet on the second 25-point machine.

After a lunch break I focused on the 10-point box, but after a couple of hours making no progress I stepped away for 5-10 minutes and switched to the second 20-point machine. By the time I took a dinner break with my wife & kids at 5PM I had a user-level shell on 20pt #2. At this point I estimated I now had 60 points. If I could either root the 10-point host or get a foothold on the 25pt before getting some sleep, I'd be pretty content with my progress.

I got back to work at 7:30PM, and looking back this is where I didn't handle my time well. I split my attention between both the 10- and 25-point machines, and in retrospect I should have chosen one of them and given it all of my attention. I wound up rooting the 10-point host in this window but it would have gone much faster if I would have concentrated on it alone.

With an estimated score of 70 points I took another 20-30 minute break around 9:30PM and watched a bit of television with my wife to clear my head. I worked one more block of time from 10PM to 12AM trying to make some progress on the 25-point hose, but to no avail. I set an alarm for 5AM and went to bed, with my goals for the morning being to either escalate privileges on the second 20-point host or fine a foothold on the 25-point machine.

# Exam - Day 2

I woke up Tuesday at 5AM, brushed my teeth, made some coffee, and let the proctor know I was ready to resume. I had decided to devote all of my attention to the 25-point host, and within 90 minutes I had found my way in and obtained a low privilege shell. I now estimated my score to be 80 points (including the lab report), so I was pretty confident I could pass if my exam report was adequate. I identified a likely privilege escalation vector, but my initial attempt was not successful.

At 7AM I decided to end my exam 2 hours early. I confirmed with the proctors, terminated my VPN, and got my kids ready and out the door for school. I then spent the next few hours using my notes to develop the exam report, and got it submitted to Offensive Security around 1:30PM. I had email confirmation that they had received the files within the hour. One week later on the following Tuesday morning, I got confirmation that I had passed the exam and become OSCP certified.

# Looking back - my advice

Overall I am very pleased with my experience in the PWK course. I had some familiarity with basic penetration testing concepts due to the time I had spent in the HackTheBox environment, but the PWK course reinforced those ideas and greatly expanded on them. The most valuable lesson I learned was how to research and be persistent.

One piece of advice I would give to anyone considering or currently pursuing the OSCP certification is to avoid being reliant on tools or scripts. Make sure you understand the fundamentals and understand how things work. The tools are great and come in handy in other settings, but for this exam I would recommend being able to do things manually and have an eye for detail as you enumerate and work on privilege escalation.
