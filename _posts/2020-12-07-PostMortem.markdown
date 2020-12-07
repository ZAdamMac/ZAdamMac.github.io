---
layout: post
title: "Let's Autopsy Jupiter's Failure"
date: 2020-12-07 11:30 -400
categories: News
---

Over this weekend, as you [may have read](https://zadammac.github.io/news/2020/12/06/SystemMeltdown.html), I suffered a catastrophic drive failure that lead to more or less burning the whole weekend on restoring my laptop to function and recovering from the failure. Amusingly, for someone who literally developed custom backup software, this went much worse than intended and resulted in the most recent recoverable state being roughly this exact date last year. Woops!

Truth be told, this wasn't [Tapestry's](https://github.com/zadammac/tapestry) fault; this whole situation comes down to stacks of operator error. Since it's my fault, I thought I'd document publicly what went wrong (and, as time goes on, how I've fixed it) in the interest of helping others avoid the same.

## Initial Moment of Failure
As I related yesterday, the principal cause of failure was as dead spinning-rust drive. The drive has a manufacturing date of 2016-09-16 and I suspect at just over four years old it's probably *just* past its intended service life.

To be fair, there is absolutely nothing to be done about failing drives. Sure, I could have been running regular health checks against it (increasing the wear on the drive) or set an alert to remind me its age, but the truth of the matter is that Hard Disk Drives fail. Solid State Drives fail. DIMMs and Processors and every other kind of component fail.

So in this case, the Hard Drive dying itself wasn't really the problem; or, more accurately, it *was* the problem, but it's not why *everything else* went wrong.

## Overcomplicating Personal Security Procedures
Early on, for whatever reason, I developed a habit around Tapestry that each computer should have its own Disaster Recovery Key, which doesn't really add to security in any way. Even more maddeningly, I was also rotating this key out each time I went through a hostname change - say if I switched the OS on a given computer.

In this shuffle, I either lost or never backed up the key that I was using on Jupiter. Sure, it had to exist and it was probably even sitting in my "file this" directory waiting for me to move it, but that drive's dead now, so fat load of good that would do me.

This meant that the one key I had was for a set of backups no more recent than 2019-12-13. This was... admittedly better than nothing.

I even state in the documentation for Tapestry that there's no good reason to use unique-per-host disaster recovery keys and every reason to only use one throughout your entire organization, so I've no idea why I did that and every good reason to never do it again.

**Fix:** Behavioral - adopt best practice of using only one DR key for the entire set of systems protected by tapestry.

## Undercomplicating the Backup File Replication Procedures
Back in April, I updated Tapestry to support SFTP. This was not a blind choice. At around the same time, I added functionality to my labmonitor device (essentially a modded Pi3A) to basically keep a particular HDD up and alive for me to fling files at via SFTP.

I tested this pretty robustly at the time and even monitored it for a month or two. Automated Tapestry backups meant that all I had to do was check around the first or second of each month and make sure the files were where I expected them to be, which was true. So after a while, I stopped checking.

That "stopped checking", by the way, is why I wanted this to be as automated as possible.

**What Went Right**:
- Tapestry can handle SFTP file transfer automatically while retaining a local copy of the files. This allowed me, in the event of an interruption, to copy the files back over later once it was resolved.
- The Raspberry Pi Model 3A *by design* will boot if it has power; this means that if there is a power interruption the device will come back up on its own.

**What Went Wrong**:
- There was a power interruption, sometime between June and July. While the Pi boots on its own and the container that ran the SFTP host will start at boot because I set it up as a service, the HDD *is not self mounting* because it was LUKS encrypted.
- This was after I had decided this "was working" and had stopped monitoring it manually.
- There was no ongoing monitoring or alerting. Tapestry WILL raise an error if something goes wrong with the SFTP transfer (which would have been the case here), but those alerts were not visible to me because the process was running in the background.

*Fix:* This will require a multifaceted fix solution:
- In the short term, the fix will be to permanently decrypt that drive and have it mount automatically based on the design of the SFTP container.
- There will also be a need for redundant monitoring: in addition to (long shelved) work to have the status displayed on the labmonitor's LCD, there should be a mechanism by which I am alerted independantly of the Pi itself if the SFTP fails to respond to a ping (or what have you.)
  - That monitoring should also include the ability to retrieve, and maybe even test, a file periodically.
- In the longer term, and to solve more problems, I need to look at ways to have the files stored offsite. One possibility is to obtain a hosted SFTP instance, but since I don't need the SFTP instance to be up often, [I was thinking about adding S3 Glacier Support Directly](https://github.com/ZAdamMac/Tapestry/issues/24). That way I can leverage some of the advantages of Amazon being way too damn big and trust only the availability of my backup files - sort of like Tapestry is intended to be used.

---

December is a stressful month, every year, and nobody seems to know why. If for some reason you enjoy PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac) or by making a one-time donation to [my KoFi fund](https://ko-fi.com/KenshoSec), which is being held over specifically for replacing this broken machine. I also have a few copies of my novel, Sanity Line, left, that I have been offering for sale with a personalized message inside, so send me an email if you want to talk about that.