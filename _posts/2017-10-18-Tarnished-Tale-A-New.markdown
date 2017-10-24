---
layout: post
title:  "Tarnished Tale To Get New Admin Panels"
date:   2017-10-18 05:00:00 -0400
categories: TarnishedTale
---

Since Tapestry[https://www.github.com/ZAdamMac/Patchs-Tapestry] is getting close to the end of its active development period and moving toward the back burner, I'm able to focus a little more attention on Tarnished Tale, that goofy little MUD system I'm building.

Most of the work - such as the recent shift from `configparser` to `sqlite3` to handle the actual working databases - has been directed at the server itself. However, I'm about to start on a new line of work (mostly to get the bitter taste of redoing work I'd already done out of my mouth) - enumerating at least one proper Admin Client for the server.

Until now, Admin functions were accessed by prepending `ATERM_MSG` to messages from the ordinary client. This is inconvenient for a few reasons, so I've set out to write two new clients - one for CLI written in python, and another html5/JS web client.

## Why Two?
Alright, so TT has a config rule to enable/disable remote access. While having admin control over the web from any machine is convenient, and the web interface will likely be more user friendly, it's possible (and even probable) that TT instances will be run on actual servers, where there won't necessarily be a graphics system.

## Why a dedicated admin client?
This was always the plan, chiefly to provide a way to pipe logs to other machines or view a dashboard of current server stats, as is planned.

Game moderation, yet-unimplemented, will still likely be done by logged-in users using the ordinary client.


