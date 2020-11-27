---
layout: post
title: "Lab Notes: PETI Tooling and the deal with Rokkakei"
date: 2020-11-25 23:15 -300
categories: LabNotes
---

Two weeks? More or less. We're on company time, after all.

## PETI gets a supported, official font editor
You might remember from past entries in this series that the only remaining piece of PETI that is original to the code I ported for the LCD display manager is the font itself. As it happens, that is no longer the case. I have already rewritten the relevant functions to eliminate the original font from the workflow, and I will be releasing both a new font and the tooling to edit these fonts along with the PETI hardware demonstrator once we're at a point where there's a there there!

### Why So Many Fonts?
Core PETI will support three seperate print-to-screen functions with three corresponding fonts to choose from - one each in 8x8, 8x12, and 16x16 font sizes. This probably seems like an unusual choice, but it actually simplifies dramatically a lot of the "graphical" driver tasks for PETI by replacing any true graphical capabilities (which would be asking a lot of the MSP430FR5994 microcontroller anyway) with a true-to-retro character-based display functionality.

However, this means supporting 3 seperate sets of 256 charcters. Obviously, this would be tedious to do by manually editing h files. As a result, I've decided to fork [Nathan Dumont's FontEdit](https://github.com/hairmnstr/fontedit), a python utility for editing monochrome pixel-based fonts, in order to add special exporter support and documentation for using the tool with PETI. Nathan has been very complementary about this activity and even went ahead and ported the utility to Python3 when he heard I was planning to use it for this purpose.

Replacing the original 8x8 font with a new font will also allow me to change the print function that uses it to more closely match the other two print functions, and improve efficiency by removing a number of processing hacks that were needed in order to use the font as it had presently existed. 

### Eta Codebase Wen, Strimmer?
Soonish. I had originally hoped to complete what I was calling the V1 Hardware Demonstrator by the end of this year, but I have been unexpectedly unproductive the entire month of November, which admittedly has set the project back further than I would have liked.

The V1 hardware demonstrator requires all three fonts to be working, along with a working demonstration that the 4-button IO is working correctly. The code is extremely simplistic and doesn't require any of the game elements to actually exist; however, it does require a schematic be created, which I haven't done yet.

Realistically, I think I can do all three of these outstanding points by the end of January.

It should be noted that this early release of the repos to the public would only really allow collaboration - none of the game code would be written then (as we're still working on the hardware primitives), and the hardware itself wouldn't even be truly operable as designed. I'll have more details on all accounts as part of that repo whenever it gets pushed into prod.

## Rokkakkei Was Rejected
In a move surprising nobody, least of all me, Rokkakkei was rejected by HTB as it is not actually a good tone fit for the platform. In an effort to make the problem more approachable for a brand-new user, it incorporates elements that are much closer to a Capture The Flag competition than what HTB normally operates.

As a result, I will be releasing Rokkakkei itself this weekend as a download available through a dedicated website; stay tuned for an announcement on that score from the [official Kensho Security Labs twitter](https://twitter.com/kenshosec).

In another move surprising precisely nobody, I have already begun work on a new box that I hope will be a little closer to the requirements of HTB. The new box is named "Extradimensional" and will be made available publically whenever it completes its run on HTB.

---

November is a stressful month, every year, and nobody seems to know why. If for some reason you enjoy PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac). I also have a few copies of my novel, Sanity Line, left, that I have been offering for sale with a personalized message inside, so send me an email if you want to talk about that.