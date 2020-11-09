---
layout: post
title: "Lab Notes: PETI Needs a Shave; Yosegi Updates"
date: 2020-11-08 23:15 -300
categories: LabNotes
---

I'm about 90% certain that at some point not that long ago I said I was going to update this blog more often going forward, but here we are, back on the 'once a month, more or less' update cadence. And on the one hand that probably bears some leverage for an apology, but on the other hand, it's exactly what I want to talk about. But before that, let's do the actual LabNotes update on both our active projects: PETI and Yosegi!

## What's New With Yosegi?
The first Project Yosegi box, Rokkakei, was submitted to [HackTheBox](https://hackthebox.eu) yesterday. There's a fairly lengthy queue for the approvals process and I'm certain that in addition to the queueing, there will be some back-and-forth as changes are made to the machine to get that approval. For the duration of the approval process and the full time the box is considered "active" on HTB (that is, prior to its movement to retired availability), I will be forbidden from posting any writeups on the build process or my attack walkthrough and logic for the box. I'm extending this to include not releasing any code for any customizations that are included in Rokkakei. I really hope that it does get approved. I've spent the better part of three months working on that box, and there were a lot of stumbling blocks. It'd be nice to talk about that in length at some point.

## How About PETI?
We have actually made small progress with PETI. Since the repos aren't public yet, I thought it would be nice at least for me to come out and share what phase we're at. I wanted to do this sooner, but I'm not moving as quickly as I would like and one new function does not an update make.

In short: PETI now has the ability to print a full-screen bitmap. I've also decided, for constraints reasons, that going forward, all graphics for PETI will be done in text mode. PETI will essentially have three fonts installed, each making 256 unique sprites/characters available. This allows me to dramatically reduce the amount of RAM that needs to be set aside for the state of the screen, since the full screen is technically more RAM than the platform would have available for modification.

Implementing those print operations will be easy; in fact, it's already done. However, testing the fonts will not be as easy, and that's where the delay is coming. I hope to announce properly in short order that we've forked an existing python tool for font design to have export features that work with the MSP430's addressing *specifically* and with the PETI font format *generally* so that the 8x8, 8x12, and 16x16 fonts can be more easily managed than by me manually computing bytes based on what I've drawn in a graphing pad. I intend to finish that, and release some new pictures, by the end of the year, which is coming up fast.

## Let's Talk Lab Time and Thinking About Goals
I've talked on this blog about productivity before. Productivity is a nebulous, Madison Avenue sort of a word, and defining what it means to you personally is important. When I started Kensho Security Labs, the Labs were about trying to spin myself up as a "serious Security Individual" as quickly as possible and *if even a little practical* find a way to supplement my at-that-time inadequate income.

But now, the goalposts have been moved before I even really came to understand that was the case. I now work in security (more or less) and I've come to learn that security is, for me, merely an *aspect* of the *well made thing*, which is the real goal. I'm not sure if a rebrand is in order, but I'll certainly be changing the way I operate in the immediate term.

Those changes will involve:
- No longer thinking of KSL as my "night job". Instead of pushing myself to be working on technical projects 5 nights a week after focusing on my technical job, I will be focusing only on one or two "Active" projects during downtime a couple of nights a week.
- In the immediate term, those projects are PETI and evaluating the need for any changes to Tapestry to suit my immediate need. Tapestry (and other projects) will still be maintained. If I think of Yosegi challenges I will produce them; if I see issues appear on my older repos I will address them.
- *I no longer need to explicitly have a security concept for the project to "count"*. While security  will still be an immediate consideration for every project, I won't be making sure that every project I undertake relates to security directly. (That doesn't mean that the introduction to hardware hacking elements will be stripped from PETI. That's still the point.)
- I will, wherever possible, have fun with my projects, and, finally;
- Not every GTD "project" in the complicated-set-of-actions sense will be a KSL project. I've been on vacation for a week leading up to a position change at work and I spent this week doing absolutely nothing outside of the scope of "playing games". That's a valid use of time.

And speaking of that last, I will be beginning a new position on the 16th within my parent company as a Cloud Applications Engineering Analyst. As with my current support position, I've been somehow skipped directly into seniority on that team. That's a stressful thought, so the last thing I or anyone needs right now is additional excuses for burnout.

---

November is a stressful month, every year, and nobody seems to know why. If for some reason you enjoy PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac). I also have a few copies of my novel, Sanity Line, left, that I have been offering for sale with a personalized message inside, so send me an email if you want to talk about that.