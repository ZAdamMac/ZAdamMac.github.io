---
layout: post
title: "Fixing Bad Xcomposite Captures in OBS"
date: 2018-04-31 13:00 -400
categories: Tech
---

While they're few and far between, I know at least a few other streamers who use OBS Studio with linux in order to livestream or screen-capture whatever it is they are trying to record. Some of you probably even use Ubuntu, and if, like me, they just upgraded to Ubuntu 18.04, there's a distinct possibility they broke some of their capture system!

## Symptoms
Using OBS Studio, Window Capture (xcomposite) Sources return corrupted video data to OBS and all points downstream. Also, sources using the [Linux Browser Source](https://github.com/bazukas/obs-linuxbrowser) plugin no longer render at all.

As you might expect these are two independant problems.

## Solving The XComposite Issue
So, many times on AMD systems, XComposite captures break in the same way. The source of the trouble ca be lead to a design change decision in Mesa, the open-source AMD graphics driver. Sometime early in the year, a design change was made to cause Mesa to output 10-bit colour information.

Now, I know nothing about graphics drivers, but I would assume the 10-bit is better than the 8 bits expected by OBS. The problem is, OBS is currently in the denial stage and essentially washing their hands of mesa, blaming the problem on the mesa developers.

Helpfully, though, the Mesa devs included a functionality to disable this. In the `<device>` block of `/etc/drirc` add the following code:
```xml
    <application name="obs" executable="obs">
     	    <option name="allow_rgb10_configs" value="false"/>
	</application>
```

This disables the 10-bit colour space for OBS specifically, meaning that the rest of your system can still benefit from the higher fidelity while OBS does't choke on itself. I found rebooting the system necessary after applying this fix, but others have reported it is not.

## Solving Linux Browser Source
This one is simple. Install `libgconf-2-4` using apt, and restart OBS.

## Closing
I hope you found this guide helpful - it was the product of a good hour, hour and a half of troubleshooting - the XComposite problem was particularly maddening.