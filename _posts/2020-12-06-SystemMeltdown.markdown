---
layout: post
title: "System Meltdown, or, Jupiter Ate My Homework"
date: 2020-12-06 19:15 -400
categories: News
---

Late on saturday night, I was preparing to do a livestream with a few friends wherein we were going to hang out and be creative in our various idiosyncratic ways. Part of this process, in this instances, involved using a *particularly janky* technologies stack to use my phone's camera as the webcam output for the live stream through discord. This is a sane and safe thing to do - I've done it a number of times in the past - and then it happened.

The camera wouldn't connect, and its client (on my computer) hung for quite a while. Undaunted, I summoned the mighty system monitor with my favourite chord for such things (ctrl+shift+esc) and... that hung for a while. Then my window manager crashed, spewing I/O error messages about the system logs all across my displays.

Then nothing.

To make what feels like a very long story incredibly short I have suffered ***catastrophic drive failure*** of Jupiter's only HDD - an old 2 TB monster that probably was past its own sell-by anyway. In the roughly 24 hours that have followed I've had a devil of a time getting an operating system back onto the beast and recovering files - for reasons that will make a funny story in the future, maybe, when I feel like laughing about it.

There are a few key takeaways. Nothing mission-critical was lost. While the most recent backup I could recover was nearly a year old, most everything I actually care about had remote backups or repos it could be pulled back down from. I'll have lost some character artwork and maybe some funny screenshots, but nothing worth crying over in the long run - though I might have a cry today regardless.

What this has brought to light for me is that I was not actually being paranoid when my mind started underlying issues with my specific deployment of Tapestry and various "blind spots" in my recovery stack - the issue was not the software as much as how I was using it. As such, here's what we can expect over the immediate term:
- I'd still like to release the PETI hardware demonstrator data by the end of the year. I've lost maybe 30 seconds work on that project and there's no reason to stop now.
- Tapestry has a few minor bugs - not responsible for my recovery failure - that need patching. I'll be taking care of those.
- A comprehensive postmortem and story about what actually went wrong and why my recovery results were so poor will be written and I will share it with the rest of you. We can laugh and cry together. It'll be fun.
- A comprehensive guide - and likely some new software tools - on how to use Tapestry as part of a more comprehensive rapid recovery toolchain will be prepared. While most people probably don't need this, I find it personally unacceptable that even with a spare HDD in my posession it took probably 18 hours of work to recover this far. This will also include best-practice information on deploying Tapestry and how to test that your backups are accessible to you and that they are *actually where you think they are* so that this sort of thing doesn't happen to you.

The other big thing this has made clear is that Jupiter - the laptop I picked up when I was still a cook to have a comprehensive machine to use for what turned out to be quite a hobby in development and technology - is past its prime. By the original date of manufacture it's nearly a decade old, and while it's still a respectable machine in terms of its performance, the HDD brings the total number of device failures on this machine up until 4. If it were a desktop my particular skillset would allow me to continue repairing it indefinitely, Ship of Thesues fashion, but the economics (and plauisibility) of that sort of repair changes in a laptop. As a result I've resolved to replace it sooner rather than later - before something harder to swap out like the video bus or SATA controller eats a fat one. Since the ODD is also toast, I suspect there's something subtly but irrevocably wrong with the SATA controller anyway, and if that goes this is a $450 paper weight. In light of this machine's cursed nature and the fact it is honestly on its way to a proper burial, I've rechristened it Sevastopol. If you get the reference, I have only this to say to you: SPAAAAAAAACE!

---

December is a stressful month, every year, and nobody seems to know why. If for some reason you enjoy PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac) or by making a one-time donation to [my KoFi fund](https://ko-fi.com/KenshoSec), which is being held over specifically for replacing this broken machine. I also have a few copies of my novel, Sanity Line, left, that I have been offering for sale with a personalized message inside, so send me an email if you want to talk about that.