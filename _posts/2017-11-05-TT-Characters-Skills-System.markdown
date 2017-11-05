---
layout: post
title: "TT: Character System - How to Skills!"
date: 2017-11-05 05:00 -400
categories: TarnishedTale
---

[Tarnished Tale](https://www.github.com/ZAdamMac/Tarnished-Tale/) is proceeding abreast as one might expect, and after a brief diversion to implement the new communications protocol (vaguely) and some improvements to the logging system we're finally into the character management subsystem in terms of the development focus!

There are lots of components to the Character Manager but today I want to talk about **Skills**. While they aren't the first thing you'd encounter on the sheet (were you to render a character in a Tabletop-playable format), Skills are what really give individual characters their flavour and what shape the overall texture of the game. I'm not going to go into a complete dissection of each intended skill because [that already exists](https://github.com/ZAdamMac/Tarnished-Tale/wiki/Skills:-Skills-of-the-Core-Set) and individual modules will add more as they come down the pipe.

What I do want to talk about has less to do with mechanics and more the overall design philosophy.

## Why 99 is the skill cap
At present skills are hard-capped at 99 because the skill check functions all compare either the actual score of the skill or a derived score against a randomly-generated integer between 1 and 100. If the score is lower than the integer, the skill check counts as a success. This is derived from the system used in Chaosium's d%-based Call of Cthulhu gamesystem.

In the future this skill cap might increase. I'm experimenting with different models for character advancement.

### Model 1: Chaosium-Like
In Chaosium's game you aquire skills via their use. If the GM believes that the application of a skill was sufficiently harrowing, interesting or difficult a "tick" is awarded and via math and dice you may later aquire more ranks in that skill.

This is similar to a system I have seen in some MUDs and other RPGs. Certain challenges are dubbed sufficiently non-trivial that completing them either occasionally awards a skill up (see Runescape) or, in some cases, a player aquires XP which they can potentially convert to skill points by spending the XP (together with time and possibly materials) at special furniture or NPCs to get "training". This model was used in several MUDs as well as in part in the Elder Scrolls Series.

I like this system because it's realistic - smashing mobs' heads in doesn't improve your skill at welding.

I dislike this system because its implementation will be complicated and possibly too arcane to be transparant to users.

### Model 2: Classless Level System
Under this system, since the base game is classless, characters can none-the-less aquire levels via combat XP or other XP rewards that periodically tip them into a new "level", rewarding them with X number of skill, however X is derived, which they can spend to raise their skills.

I like this system because implementing it would be simple.

I dislike this system because it lacks a little for realism and isn't quite the direction I wanted to go with the game at large.


## Skills at Character Generation
At character generation, regardless of which advancement model we're going with, player characters are awarded a pool of spendable skill points derived from their Intelligence(INT) aptitude, and are able to spend those points to raise their skill scores, probably using some form of diminishing returns model to prevent players from starting with absurdly high scores in any one skill.

Further, the skills themselves all have an associated BASE score, defined in `$mod-skills.dat`, which will be awarded to them immediately, before point buy begins. While the skills themselves do not support racial adjustments, racial adjustments to aptitude will affect aptitude-based scores.

## But what skills are "Core"?
Hard to say. Core will certainly include all the combat skills and some obvious basic skills like Concealment, Agility, Locksmithing, and so on.

Core will also have to include at least some rudimentary crafting mechanics. Crafting is pretty much a staple of RPGs as a metagenre and it was always intended to have both crafting in general and and future support for an eventual EVE-Style Deep Economics Module.

Where it gets interesting is settling on esoterica. Most games have a pick-locks mechanic, but Locksmithing also allows two other abilities: `Impression`, which generates a key for a given lock; and `enlock`, which, coupled with a Locksmith's Tools and Pinning Kit can attach a lock to a door or container.

But how should crafting look: 2-tiers or 3? And how about truly weird skills. Should Gemmatria be a skill, or hold off until the Magic Module? How about an analogue to DnD's Knowledge? Nothing's stopping players from simply looking up lore - wouldn't it be a waste?

These are the questions that we're still trying to answer.

*Tarnished Tale is a FOSS project with no proper revenue module. If you enjoy watching its development or would like to contribute, feel free to open a Pull Request or contribute to [our Ko-Fi fund](https://www.ko-fi.com/PSavLabs)*
