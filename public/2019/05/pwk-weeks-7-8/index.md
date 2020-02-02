# PWK: Weeks 7 & 8


It's hard to believe I'm already starting my ninth week of the PWK course, it's gone by extremely quickly. I took a few days off at the end of week 7 for some time away in Arizona with my wife, but I've been back at it for a few days now. Here's an update on my progress.

{{< figure src="/content/images/2019/05/week7.png" >}}

### Day 44

* Rooted box #16. Recon, enumeration, and attempted (read: failed) exploitation of #17. It turns out I was using the wrong hammer.

### Day 45

* Completed #17. I had been trying to use an exploit that the machine has apparently been patched against, but something related still worked. I did wind up using Metasploit on this one. Also did recon/enumeration of #18 and got admin credentials for a web app.

### Day 46

* Completed #18. This machine reinforced that I need more web application experience. I got lucky choosing the final exploit but need to find out how to fingerprint a client browser via XSS. Started scans/recon of #19.

### Day 47

* Completed #19. Here I learned some new PHP syntax and feel more confident after quickly noticing how I needed to chain two vulnerabilities together. I decided to go after another "big 4" host for my 20th target and performed initial scans against Sufferance.

I was traveling with my wife on days 48 through 50, we had some time away from the kids and I was able to get rested and came back refreshed. It was a well-timed break. It was a little tough getting motivated right away, but I wanted to finish what I started with Sufferance. Here's a look at my eighth week.

{{< figure src="/content/images/2019/05/week8.png" >}}

### Day 51

* I revisted recon and enumeration against #20, Sufferance. My VPN was being problematic, but I was at least able to identify how I expected to gain my initial foothold.

### Day 52

* Completed Sufferance. I actually worked through this one pretty quickly. I got the low privilege shell without too much trouble and found the correct path for privilege escalation, but burned at least an hour for not knowing the right options for compiling a binary.

### Day 53

* Started and completed host #21.

### Day 54

* Completed initial recon and identified exploitation path for #22. This box was heavily dependent on another lab machine, and was probably the most fun I've had in the lab so far. Thankfully I had already rooted the dependent host, but I did have to get back into it and do a lot more post-exploitation work.

### Day 55

* Completed #22. Learned quite a bit about client-side exploits with this one. Started recon/enumeration of #23.

These two weeks were pretty light on lab time since I was out of town, but I feel like I learned quite a bit in a short amount of time and completed some of the more challenging lab machines. I am closing in on my goal of 30 completed machines, and hope to get there in the next few days. I'm now over 100 hours into the lab with a total of 105.5 invested so far.

_Photo by [Christoph von Gellhorn](https://unsplash.com/photos/kjA82leKmJc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/arizona?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)_

