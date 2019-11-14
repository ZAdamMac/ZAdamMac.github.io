---
layout: post
title: "Enumpi Project Update: Finally Making Headway Edition"
date: 2019-11-15 15:00 -300
categories: Projects Enumpi
---

Around this time last year, I embarked on a project to create a set-it-and-forget-it network enumeration device using the Raspberry Pi 3 platform, which I detailed in two guides, [here](https://www.kenshosec.com/Guides/enumpi-1.html) and [here](https://www.kenshosec.com/Guides/enumpi-2.html). While the version of the EnumPi that this project produced was roughly suitable to the intended use case, the project itself really got me thinking about generalizing the solution and making it a bit more robust.

To crib Douglas Adams, this was broadly unpopular and is widely considered to have been a bad move.

The general-solution design I've played with since went through three or four major iterations, some of which were even diagrammed with help from the lovely Kat @ [CMYKat Designs and Illustrations](https://www.cmykat-designs.com/), before eventually landing on the current implementation plan. A full overview of the environment I'm creating is something I'm still working on writing up and diagramming properly - eventually it will be published to [the main Kensho website](https://www.kenshosec.com) as a Projects page. As a general overview to make the blog post a bit more comprehensible, the overall goal is to create a Raspbian-Derived image that can be deployed in swarm fashion to any number of devices attached to a target network, all of which share a minimal central control system.

For those who have been playing along at home though, following [@pAtchSavage87](https://www.twitter.com/patchsavage87) on twitter, I wanted to put together an update on the current state of the project's three main components: **EPiC2**, **Enumpi-Helpers**, and **Enumpibian**.

The current goal is to have a functioning prototype of the bare-bones capabilities of the platform by this time next year, for the incredibly vain reason of wanting to present the work at the local BSides conference.

## EPiC2 - Enumpi Command and Control Service
One of the design goals for the redesigned Enumpi concept was a remote command and control concept that was a bit more sophisticated than "just SSH into the Pi to control it." This was one of the greatest sticking points in successive design iterations, but I've finally come up with a balance of remote and local control concepts that I actually like. Working on EPiC2's development has been the main focus of work for this project over the spring and summer of 2019, and now that it's actually started it's coming along gangbusters.

[EPiC2](https://github.com/zadammac/enumpi-c2/projects/1) is a RESTful API, ultimately intended to be served from within a container, that wraps a file dump and a MariaDB MySQL-compatible database. The database is used to store minimal information about the authorized users of the network, connected enumpi devices (known as "clients"), and minimal messaging surrounding the issuing of and response to commands for individual hosts. The file dump is used for two-way communication, both hosting files (likely scripts to add functionality to an enumpi device), and accepting inbound files (nominally, reports and other data returned by the Enumpi in question.) Everything is to be wrapped in TLS and authenticated using a JWT-based authentication scheme, roughly based around OAuth2. An in-browser administrative interface is also planned, which will handle some of the application logic as well.

Right now the current state of EPiC2 is actually pretty robust. With the exception of the file transfer system, the bulk of the core API functionality is completed, with only the core command-relay functionality left to be tested. Development of EPiC2 will likely pause once the file transfer process is complete so that work can begin on the other core components of the environment, with the web interface being designed only once the client-server communication system is validated fully.

As a matter of course the EPiC2 prototype is *not* a fully-featured implementation, and exists more as a proof of concept. In addition to the features that are a part of the current implementation plan, work will need to be done after prototype validation in order to provide some better deployment methodologies, stronger usage audit controls, and possibly some level of command automation - the ability for some client triggers to be relayed across the whole swarm.

As it happens, I've not got the strongest skill set in web front-end development, and while I'm happy to learn it and this seems to be a good project to start with as any, if anyone out there is a web front-end dev and wants to contribute, [drop an email.](mailto:zadammac@kenshosec.com).

## Enumpi-Helpers: Python Module.
You might know by now that I have a bit of a thing for writing and distributing python modules - it should come as no surprise that I want to design a simple module to facilitate interaction with the EpiC2 API. Work on this module package has stalled - obviously, having an API to integrate with is going to be a key step in setting up this kind of project!

Of course, Enumpi-Helpers is also going to have to be more than merely a client-side wrapper for the API. It's also going to be the project under which a very critical piece of the Enumpi Ecosystem is developed - the dispatcher. The current operating concept is going to be for every device to have, registered as a routine cron operation, an Enumpi script known conceptually as `dispatcher.py`. This script wakes up, reads various environment variables, and uses them to reach out to the EPiC2 instance it's configured to listen to. This script then pulls down the latest set of commands, schedules them (using either cron or some other delay mechanism for one-off jobs), and handles sending the output of those commands back to EPiC2.

The final key component of Enumpi Helpers is the Reporter. This needs to be both a cron-triggered element and something other scripts run by the enumpi can arbitrarily trigger, in order to access the local results DB, run reports against that DB, and send the report files back to the C2.

## Enumpibian
Enumpibian is a working title for a fork/extension/package, based on Raspbian OS (itself derived from Debian) that would contain everything the Enumpi device itself needs, in order to facilitate the rapid setup and credentialing of each device. This would contain any extra packages used (the DB engine, `enumpi-helpers`, dependencies) out of the box.

There's also some OS-level functionality that needs to be set up just right to get the device working, all of which would be contained in this image. `helpers.Dispatcher` needs at least enough `sudo` permission to add and remove cron jobs, for example. The whole device is intended to be initialized using grenade-style credentialing - pulling encryption keys and other credentials off of an attached flash drive that is then removed from the device.

Enumpibian itself likely isn't part of the target deadline for the prototype, but will be an evolving target as time goes on.

If you find this or any other project we're working on interesting or helpful, or want to help support Kensho Security Labs, I'll freely admit that coffee is the ultimate debugging fluid and your donation of a cup to me would be appreciated greatly:
[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/A8614WZ3).

ZAdamMac - the developer at Kensho Security Labs, is also sponsorable through the[GitHub Sponsors Program](https://github.com/sponsors/ZAdamMac). For the first year of the program, Github is matching your sponsorship pledges - so they're basically like half-off!