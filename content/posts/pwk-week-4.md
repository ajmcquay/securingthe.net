+++
author = "AJ McQuay"
categories = ["OSCP"]
date = 2019-04-17T19:31:39Z
description = ""
draft = false
image = "/images/2019/04/jared-brashier-252015-unsplash-1.jpg"
slug = "pwk-week-4"
tags = ["OSCP"]
title = "PWK: Week 4"

+++

It's already been a month since I started the PWK course, and this past week was my first week fully dedicated to lab machines.  Some have been easy, some difficult, but all have taught me at least one takeaway lesson.

Here's what my daily time commitment looked like in week 4:

{{< figure src="/content/images/2019/04/Screen-Shot-2019-04-17-at-1.06.59-PM.png" >}}

## Day 22

* It seems I chose (at random) a machine in the top half of the difficulty spectrum for my first target, yay.  We'll call it box 01.  I spent 3 hours here on reconnaissance, enumeration, and some failed exploit attempts.

## Day 23

* I finished exploitation of box 01.  I had found a way to add a user to the system and add that user to the local admin group, then spun my wheels trying to upgrade to a system shell.  I finally asked for some guidance from fellow students and they kindly pointed out that I was done and was just overthinking it.

## Day 24

* I decided to spend time enumerating DNS for the entire public lab network so I had the hostname documented for every machine.  I exploited box 02, but went to bed before starting any post-exploitation.

## Day 25

* I did some post-exploitation snooping on boxes 01 and 02.  Got a low privilege shell on box 03 and started looking for privilege escalation opportunities.

## Day 26

* Finished up exploitation of box 03.  This one taught me a LOT about Windows privesc.  Also owned box 04.

## Day 28

* After taking Friday night off, on Saturday I completed box 05 and unlocked access to the IT network.

I averaged just over 3 hours per target in my first week, though it seems many of the hosts I went after are in the middle to lower difficulty range.  I plan to spend at least one more week going through machines and picking up tricks and techniques before attempting any of the "big 4".  I'm feeling more confident though, and my goal of compromising 30 hosts before my lab time expires seems well within range.

I also have my exam scheduled for 5/21, but I may book at least a 15-day extension and push the exam off a couple of weeks if I stall in the labs.  My total time commitment so far is up to 55.5 hours.

_Photo by [Jared Brashier](https://unsplash.com/photos/6WPcf5D1XdA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_

