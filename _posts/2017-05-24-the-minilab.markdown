---
layout: post
title:  "The Minilab: Moving Forward"
date:   2017-05-24 12:40:06 -0300
categories: Theory
---

Computer science, in general, is one of those fields where the more learning you do, the more you want to do. In a lot of ways you can go on in this way indefinitely. There's always something new to build, some new function to uncover, some new software tool to play with.

However, eventually you get into concepts that go beyond scripting. You're going to need an IDE. You're going to want to do some network concepts. You might want to work with new or exotic hardware. For that, you go beyond a few extra directories and a piece of software or two. You need to build a lab.

I like labs. In general I like any hobby with a specific set of tools, particularly if those tools are used to make things. I hesitate to call myself a maker, because I don't, presently, actively produce physical objects. That might change.

For a while now I've been banging around the idea of the Minilab - my solution to having half an office to use as a lab for all the various areas I want to explore. The plan calls for a variety of minilab toolsets, but I'm going to talk about the ones I intend to aquire first: the CompSci Minilab.

For reference, here's the current state of the minilab:

![The Minilab](https://pbs.twimg.com/media/C-xr3U7XYAADfin.jpg:large)

### Patch Savage Labs - Minilab 2017 Upgrade Plan

#### The Main Box
Okay, so presently, my main-and-only box is an aging Acer Aspire, a student-level model circa 2009 or so, running Ubuntu and not useful for much apart from browsers, chat, and an IDE session. It's really starting to show its age - the mouse and keyboard visible on the shelf under it are actually necessary to operate. Not ideal for the man on the move.

I do however have most of a defunct gaming computer - a homebuild I was calling Gliesse - which I'm liquidating. Take that and some diverted income and I intend to upgrade to something new from the **Lenovo Thinkpad** line, probably a mid-range enterpirse model.

With the new hardware for the main box, we're going to strip off the pre-installed win10, which we don't need. We'll reinstall with Ubuntu and keep a few other OSes on hand (with Gliesse decomissioned I now have a Win7 license freed up that had its own Win10 upgrade option already used, plus probably Kali and maybe some Qubes to play with).

In addition to simply being beefier overall as a machine, the specs for the main box have been chosen specifically to allow me to impement something like [The AVATAR Project](https://www.blindseeker.com/AVATAR/) with some modifications. I intend to use VMWare Workstation Pro, which is going to be an extra expense but seems to be the most expedient way to make it happen. This will give me a small virtual network to play with, segmented into Management, Guest, and Office subnetworks. I can play with a Kali VM to practice penetration techniques (and detection techniques) from Guest to Office. I also intend to load at least one Win7/Win10 VM into the office network to test my builds for windows compatibility. Best of all is I can bring the individual VMs online without having to bring up the whole network at once, saving resource drain.

I'm also starting to feel the need for an IDE when working with Python. Tapestry and some other automation work I've done has been easy enough to develop in a raw editor (Notepadqq), but TwistedTale is evolving to a point of complexity where I need to be able to better probe the script while executing it.

#### The NAS
This old laptop (affectionately, the Slaptop) isn't going in the bin just yet. I intend to set it up as a NAS, having as I do several TB of old HDDs that would be relatively inexpensive to turn into external drives. Yes, I realize using USB drives for a NAS is far from ideal, but I don't need to handle huge amounts of bandwidth at once here. Simply having a place to store the bulkier files will be fine, given that the Main Box laptop will be losing several tens of GB to OS images and VM snapshots already.

I still haven't decided if I want the NAS to be world-facing in some way (as a private dropbox) or just facing my household network. Both have their pros and cons, but in order to make a decision I'll need to learn more about the related concepts.

#### Residential Server 1
Contrary to the name, this will not actually be used for storage, but I have determined it to be the cheaper and likely more convenient alternative to handling my other domain, [sanityline.net](https://www.sanityline.net) over my present arrangement of hosting. Depending on feasibility I may use it to host TwistedTale and/or this blog as well. The machine will likely be nothing special and will in fact likely be something of a Pawn Shop Special.

At the present rate of revenue and expenditure, and using old equiment to the maximum, I expect this phase of development to conclude around January 2018.
