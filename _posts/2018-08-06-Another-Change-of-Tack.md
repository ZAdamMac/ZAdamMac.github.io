---
layout: post
title: "Change of Tack: The Future of Tapestry"
date: 2018-08-06 21:00 -300
categories: Tapestry TarnishedTale
---

# Why I Do This
*I got into software development the long way around, but I did it for a reason.*

I have long been a "right tool for the job" kind of guy. Doesn't matter the field - I was like that as a kid with my marksmanship, and then later with my cooking, and now, with tech, it seems no different. The problem is defining our terms.

When I set out to create Tapestry, I set out with a simple goal in mind - take my overbearing backup creation process and streamline it in such a way that its operation is as close to seamless as possible. And I've achieved that! But then, in creeps the contemporary tendency to try and monetize one's hobbies. I started trying to productize Tapestry. To a certain extent, this was positive - that's why I tuned down the system to use multithreading, its why I improved the security model, it's why there's still a laundry list of features to implement.

# What's wrong with that?
The more of these features I try to implement, the more and more commoditized Tapestry becomes. The more like every other backup solution it gets, the more I find myself asking why anyone would use Tapestry over, say, the Windows backup utility, or Apple's Time Machine.

At this point in time there's no evidence to suggest anyone apart from me is using this project at all. I get a few hits on the codebase now and again, I suppose. I know my new bosses looked it over before they decided to hire me.

But that's sort of the point - even if I added in every other feature, the big kids are going to outcompete me. They'll have faster update turnarounds, better QA, the whole business.

# Stallman Was Right
I'm not sure it **was** Stallman, actually, but *somebody* said that the philosophy in FOSS ought to be that software should do one thing, and do it well. So that's the focus on Tapestry, now. Tapestry is going to create and process its backup files. And it's going to do it as well as I can figure out how to make it do.

# So, what's next for Tapestry?
Not Loom. Maybe not even a networked storage protocol, beyond setting the output to a place on an NFS. When I started this project, I failed to grasp how in bed the business world was with cloud storage. At my new job, the vast majority of my working files don't even live on the metal in front of me - they're all stored in the cloud.

That said, there's still a reasonbly long list of projects to tackle for Tapestry. It's still broken on windows systems because of the way I implemented the multiprocessing feature. It's still not as responsive as we would like. It still lacks any sort of completeness checking for the backups themselves. Most of these features and fixes, however, are fair less intensive to implement than a whole new networking protocol and the webapp to go with it.

## And the Labs in General?
Eliminating loom - an unnecessary feature bolted onto the side of software already arguably more complicated than it needs to be - frees up more time than you might expect. Time to spend on fixing the parts of Tapestry that actually need it, but also time for other work. I still want to at least prototype Project Clair. I still have a side-hobby in practicing HackTheBox exploits. And, with some new revelations in how the MUD engine I've chosen to work with works - it's worth it to do a new, redesigned version of Tarnished Tale. But that's a topic for another time.

*Appropos of nothing, I'm doing a Charity Live Stream in November, where I'll be playing a game. [Donations are for the Children's Miracle Network!](https://www.extra-life.org/index.cfm?fuseaction=donorDrive.participant&participantID=315996)