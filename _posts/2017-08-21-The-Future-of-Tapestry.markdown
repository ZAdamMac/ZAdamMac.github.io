---
layout: post
title:  "The Future of Tapestry"
date:   2017-08-21 00:40:00 -0300
categories: Tapestry
---

Two months ago, I announced that [Tapestry](https://github.com/ZAdamMac/Patchs-Tapestry) v.0.3.0 was "coming out soon". And that much is true. What is less certain is what v.0.3.0 is going to look like - and how much that future is going to have to change.

I have always been developing Tapestry under as generalized a view of the problem it is meant to solve as possible - in order to make it a useful tool rather than a simple bit of automation to hide forever as a cron job. However, I'm concerned I might be making it more general than usefulness dictates - making things work just to have them work.

One feature, long intended to implement, has been operability under Windows. This feature wasn't born out of marketing - at the time, I actually did run a Windows machine alongside my various linux devices, and it seemed useful to have Windows under the same protective umbrella as the rest of my boxes, even if it was becoming increasingly clear that the Windows Machine was a toy. v.0.3.0 was meant to implement Windows support in full, rather than as a stack of hacks I'd deployed on one machine.

However, after almost three months of development (in reality, about 30-40 hours), I'm wedged *good*. Windows support requires a complete restructuring of the software - software that had taken so long to get functional-in-full under Linux. The problem is non-trivial to solve.

So, with little information on how useful the cross-platform use case actually *is*, I'm stuck on whether or not I want to focus on fully developing Tapestry under the operating system I actually use (Gliesse, my windows machine, is now lost to the ills of computer rot), or on making Tapestry somehow "marketable" when I know that my recommendation, as a security nerd, for the time being is that you use something professionally developed rather than my passion project.

As it stands, **`dev.py` can be assumed to be the linux version of v.0.3.0**, and works as expected in all the use cases I can think of. As before, I am actively using it for my regular backup schedule. 

## And Tarnished Tale?
Going smoothly, actually - I'm working its development over when I have spare time but not access to my windows VM.

##Coming Soon:
Another project semi-announcement, and musing on transitioning careers so dramatically.
