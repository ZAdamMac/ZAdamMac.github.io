---
layout: post
title: "PETI Design Docs Made Public"
date: 2020-12-24 11:30 -400
categories: News
---

Just a few moments ago, I finally set (the PETI and PETI-Helpers repos)[https://github.com/zadammac/peti] public! This update has been a long time coming, but it's also not the end of this project. In a way, it's merely the beginning.

The current state of PETI is pretty simplistic, and if you've been following along on the blog, you won't see many surprises. It's a pretty basic proof of concept, demonstrating the bare minimum of the display driver functionalities and a minimal POC for the button circuits I intend to use as user input. Doesn't do much - prints a few messages, runs a count-up timer, and shows you some button states, all on the display. Check out the repo itself for the source code as well as a listing of the parts you would need to get started develping against it.

### What's next for PETI?
It's tempting to answer that question with "The Whole Damn Project", but of course shorter-term actionables are always preferred to long-distance goals or targets.

I've been down the road of building complicated things a few times now, and the experience has taught me that nothing is as useful to a convoluted project as a detailed architectural plan before you start writing code. If I'd done this early on for Tapestry or TarnishedTale I would have avoided the issues that lead to the several giant refactors that have been part of Tapestry, and it was such a refactor that killed TarnishedTale, though I may return to that project at some time in the future.

Fundamentally, that review is going to focus on a couple of important points:

#### Finding the Structure of the Main Loop
The current structure of the Main loop is... inefficient. I rely on a lot of functions happening inside ISRs and potentially interrupting each other - if you play with the POC you may discover that this causes some bugs. It also means that the display updates happen more or less ad hoc and are therefore rather slow.

I want to define the main loop well in advance so that game adjudication, display state changes, and any other functions happen in an orderly way, relying on ISRs to a minimal degree, for a smoother experience.

#### Defining the Game Adjudication Structure
Fundamentally, PETI is a game. I'll want a clear game plan (heh) on how to implement it so that there will be a minimum of refactoring. We need a way to both trigger and track changes to the game state over time and a clear idea of how to store that game state in as compact a way as possible. This is also the time at which I need to deside which features from my gameplay wishlist are core to PETI and which might be expansion goals or future releases.

#### Improving the Display Management
While the current display management is pretty ad-hoc, I actually have a plan for a more rigid layout, possibly including the ability to selectively update parts of the display only as needed and with a minimum amount of calculation. Writing to the full screen currently takes about a full second, which is somewhat suboptimal. It would be better, by far, to be able to gang up all writes to the screen to execute in one operation, which means abstracting yet again above the individual PrintText commands and adding a way to track the full state of the entire screen.

This will also include something I haven't talked about yet: "models". I want a way to define a specific character-set to belong to a particular character (in the gameplay sense, rather than the display sense) as a block to make addressing easier. It's a bit of a stretch goal but if I can get it working it should simplify the work of animating character models on the display.

This will also mean fully defining (or at least further defining) the iconography fonts (8x12 and 16x16).

#### Roadmapping the Rest of the Hardware
There's a lot left to do in terms of the hardware design. Keen-eyed viewers will note that the schematics included in the Repo at the moment only correspond to an inputs daughterboard. Obviously, at some point we need to design a full board, including whatever kind of programming capability will be in the final device, an independant power supply and the attendant hardware for that, and some additional output hardware.

I have a lot of ideas for how to execute this and what is missing in terms of functionality on the hardware level, but turning ideas into a project requires some planning that I frankly just haven't spent the time on.

### When can we expect this planning to be done by?
I intend to complete the planning by the spring of 2021, if not sooner, as I also plan on dropping a number of projects to once again focus on one or two quality work items, PETI included. If you want to contribute to the planning, there's a number of things you can do:
- Check in on the Repo periodically. Like with Tapestry, I will be maintaining both Project boards and a Wiki that details some of the build notes (once I do the on-paper work to make them coherent.)
- [Join the Kensho Labs Discord](https://discord.gg/34KJfkg) using this link. It's a quiet place but I will have a channel there for PETI brainstorming and updates, in much the same way I used to do with Tapestry.

---

That concludes our work for Q4 of 2020. If for some reason you enjoy PETI, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac) or by making a one-time donation to [my KoFi fund](https://ko-fi.com/KenshoSec), which is being held over specifically for replacing this broken machine. I also have a few copies of my novel, Sanity Line, left, that I have been offering for sale with a personalized message inside, so send me an email if you want to talk about that.