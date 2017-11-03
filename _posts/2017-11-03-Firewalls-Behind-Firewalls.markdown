---
layout: post
title: Firewalls Behind Firewalls: A Praxis Adventure.
date: 2017-11-03 09:35 -400
categories: Praxis
---

With [Praxis](https://www.psavlabs.com/Projects/praxis.html) the original goal was always to eventually be able to clone setups. In this week's case, I was trying to clone the setup I was using for Polaris, the server that hosts my vanity website. I was planning on introducing other elements to that server over time, both public-side and behind the scenes, and it struck me that nowhere in the professional world would you ever experiment by tinkering with Prod.

So, I set out to use Praxis, which was originally built with the help of [this fine book by Tony Robinson](https://www.amazon.com/Building-Virtual-Machine-Labs-Hands/dp/1546932631) which I highly recommend. I knew nothing about virtual machines going into the project, but between his book and a little bit of googling for the assumed predicates I both got the "prototypical" version of his network and a few other derived formats set up very very quickly.

Naturally I assumed setting up Polaris-β to be just as simple. I should have known otherwise when I discovered that even cloning the machine itself was tricky, but that's a topic for another time. With a little over an hour's work, I set up a fresh Ubuntu Server vm runnning 16.04 LTS with all the updates (just like Polaris), moved the Floating Ubuntu Desktop Install onto its subnet, and created a new pfSense2 VM to control that network.

The intended final result looked something like [this](https://pbs.twimg.com/media/DNp8qQUW0AU4Ply.jpg:large), with the "dummy" version of polaris and the floating "laptop" VM moved behind this new router, which was on the far side of the IPS's AFPACKET bridge, so that if I chose to run attacks against the server with Kali to simulate an out-of-network attacker I could very easily log everything with the IPS.

Naturally, this didn't work right out of the box, and after spending an hour or two double checking the firewall rules on both pfSense machines, and a few hours of triage with Mr. Robinson's help, all I had was that DNS definitely didn't work.

This should have been clue enough, but frustration-burnout was making me stupid and so I didn't realize the issue until the following day - there were two minor settings which both had to be corrected, both on the secondary pfsense2 vm.

### The Solution:
On pfSense2 (secondary), disable the DNS Resolver and enable DNS Forwarder instead.

Then, under SETTINGS>ADVANCED, click the Miscellaneous tab and input the settings for the primary machine's SQUID proxy.

Couple reboots later and everyone could talk to the internet just fine, leaving me with the tedious tasks of configuring the new server VM to be set up just like Polaris, and configuring the new router to have the same firewall rules and port forwards as the production one.
