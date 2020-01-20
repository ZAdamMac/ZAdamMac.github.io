---
layout: post
title: "The Future of the Enumpi Project"
date: 2019-12-19 23:00 -400
categories: Projects Enumpi
---

It's been a little over a year since I embarked on a project I was referring to as Enumpi - a project that's been through the better part of half a dozen design iterations since then. I've talked about Enumpi here before - repeatedly - and at length regarding its design and how the project had become complex enough to warrant being handled as three seperate projects.

Not bad for something that started as a few different chron jobs.

Some might remember that the impetus for creating Enumpi was less around creating a marketable network enumeration appliance and more around solving a very niche issue I had - the need to passively enumerate a given practice network.

Since then, the practice network I use, [hackthebox.eu](https://hackthebox.eu), has changed its uptime model. Instead of machines constantly being up on a given VPN, the machines are now more or less only up when users are actively working on them.

While I could still passively sniff a given network to pre-collect my enumeration data, the fact remains that it would still be a more elegant solution to build up an enumeration script running on my lab machine, and kick back and play some good old-fashioned GBA games on the raspberry pi instead, with that kind of model.

That said, I've kept my design notes for the system, and the C2 component remains nearly complete from an API perspective. If you have a need for the code, let me know - I would be happy to collaborate on such a project, or perhaps even just release the code rights.

This change allows me more time to focus on security research, and more developer-gumption to be spent on Tapestry, for which the next release will be fairly feature-rich (thus the delay). If you find any of my projects useful, they can be sponsored by backing ZAdamMac in the [GitHub Sponsors Program](https://github.com/sponsors/ZAdamMac). For the first year of the program, Github is matching your sponsorship pledges - so they essentially count for double!