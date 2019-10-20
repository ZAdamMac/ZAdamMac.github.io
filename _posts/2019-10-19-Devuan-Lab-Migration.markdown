---
layout: post
title: "KSLabs Upgrade: Deploying Devuan"
date: 2019-10-19 22:30 -000
categories: KenshoSecurityLabs
---

One of the great things about the most popular linux distros for the desktop right now is that they all work more or less out of the box. This is great for people like me, who are just looking for a quick, relatively free, no-cost OS in order to get up off the ground and start working right away. For a few years now, my daily driver had been Ubuntu. But with the 18.04 LTS release, I started becoming more aware of the limitations of the operating system. For the past few months I've been pondering a change, and today, I bit the proverbial bullet and transitioned my daily driver, now known as Nostromo (a somewhat-modified Lenovo IdeaPad 310), to [Devuan](https://www.devuan.org).

## Why Change?
Much of what I try to do by way of customization - what *anyone* tries to do in that regard, really, is gated behind a portion of the operating system known as the Init System. An init system runs as PID 1 - it's the first program to come up during the launch of the OS - and it manages basically everything else your OS can do.

In such an environment I'd normally want something proven, known-durable, and - this is crucial - easily-configured. I found, more and more, that I didn't get that with `systemd`, the init system of choice for just about everything. It's in Debian, the parent OS to Ubuntu, and pretty much all the other big-name distros.

My annoyance with systemd started with an upgrate at some point in the last few years that moved device naming standards away from the expected (wlan0) to something I believe was meant to be based on some form of pinning, but just made things hard to remember (wlp2s0). It's a small change, but it's frustrating.

Additionally, in the GNU/Linux ecosystem, there's a design philosophy that runs, more or less, as "Do one thing, and do it well." This is good advice generally, but especially when considering the design of system components which need to be lightweight, reliable, and table. systemd is not that. It's woven its way from being an init system into controlling all manner of aspects of the system itself, standing in the way of most core system components. Recently, [systemd was announced to be eyeing a takeover of directory management](https://www.theregister.co.uk/2019/09/25/systemd_inventor_home_directories/) regarding the `/home/` directories, no less.

This was a bridge too far, and moving away from Ubuntu onto something else got bumped from Someday/Maybe into General Lab Tasks, and I chose the arbitrary deadline of the end of this month to finish the work.

## Why Devuan?
To be fair, I gave quite a few other possibilities a bit of a shake, trying to see if anything else met my criteria. There are a lot of good distros out there that don't use systemd. However, some of them are so into the weeds that they only kind of count as distros (like Gentoo, which has such remarkable freedom as to leave the user only slightly better off than sitting in front of bare metal), and most of the others seemed to be more focused on server deployments, requiring extensive work to get a desktop environment up.

Devuan was the shining light in all of this. It's a systemd-free fork of Debian, making it a close cousin to my native Ubuntu, and something that I felt would be easy to learn. Critically (for me), it shared a lot of the same configuration logic and the same `apt` package management as Ubuntu. While it didn't (seem) to have direct support for Weyland, it did have an extensive set of Desktop Environments to choose from right out of the box. I span up a VM and was pretty quickly comfortable and satisfied that this would be workable, so I chose another arbitrary deadline and decided to start the deployment with my daily driver machine on 2019-10-19, because it was a saturday and I was unlikely to have anything better to do.

## Step 0: Preparing to Move
Since I was moving from one OS to another without changing physical hardware, I decided it was probably prudent to do some backing up. To start with, as an absolute fallback, I pulled a full `--inc` backup using [Tapestry](https://github.com/ZAdamMac/Tapestry) as a fallback, and threw it in the usual location. I also went ahead and pulled essentially everything copyable out of /home/ and threw it on a side-drive I had available that was ext4 format, so that the permissions will be held alike. This *mostly* worked - more on that later.

Whenever you're doing something like this it helps to have a plan, as well. I had a play-by-play restoration guide intended for restoring my work environment on a fresh install of ubuntu 18.04. While I've learned in this process that I missed a few things on that list, this was invaluable. In particular, it helped to know:
- What apps I use often enough that I'm going to want to install them right away.
- How to install those apps:
 - Do I need to add new ppa records to `/etc/apt/sources.list`?
 - Do I need to grab debian packages from the builder?
 - Do I need to build from source? What are the dependancies?
 
 Finally, I took the same Devuan ISO I had used for validation on my VM and burned it to a USB stick using Ubuntu's native startup disk creator. I used the "DVD" ISO, available [here](https://www.devuan.org/get-devuan) - this is the only version with the ability to pick between DEs easily, which was important to me, as I had already settled on using MATE.
 
## Step 1: Initial OS Install
I want to preface this by saying that my threat model is not the same as everyone else's. I develop software intended to manage privacy, but I don't travel much, and my laptop is almost always on my person. I went with a level of hardening that goes a step beyond the base install. That said, I consider this level of caution to be the *minimum viable protection*. If I was setting a computer up for my Grandmother, I'd probably do it more or less the same way.

Thankfully, this version of Devuan has a graphical install process based on Debian's graphical installer. If you've used either Debian or Ubuntu, or another cousin of both, Kali, you'll be familiar with the look and feel of the install process.

There wasn't much to be said about install. Most of the graphical install is making sure the system knows where you are (for package mirror selection, localization and keyboard config purposes), when you are (to set the system clock), how to connect to your network to install packages, and a bit about partitioning. I went with using a single partition for everything - if I ever needed more space for /home versus /var, /usr, and /bin, I'd need new physical hardware anyway. I also made sure to select the encrypted LVM option, which uses LUKS. I know some people are leery of Full Disk Encryption, but I consider it an essential practice.

There were a few points of consideration during the base install which I wanted to mention since they're security-adjacent and that's sort of what we're all doing here:
- Unlike Ubuntu, Devuan lets you log in directly as root. During install, you're asked to set a default password for root - if you don't, it instead makes the first user account created root. My recommendation is to set a password for root; the system will then prompt you for information to create the first user separately. This turns privilege separation into something of an automatic process, and is only a little bit of a hamstring - when you've logged in as the daily driver user, you'll have an option to use a `Root Terminal Session` via XTerm in order to perform administrative functions.
- **If you select Encrypted LVM - and you should** - the installer will first try to wipe the drive. This is recommended if for some reason the drive is brand new to you. In my case I skipped it by pressing "cancel", since I was installing over a known-more-or-less-clean system.
- The prompt for your LUKS passphrase comes after the deletion phase. While it's possible to map other authentication methods to LUKS, that's not exposed during the OS installer phase. As always, choose a good strong password, as long as you can comfortably type blind. We recommend passphrases with special characters and numerals.

Once the OS install otherwise completes (which in my case took about an hour and a half), you'll be prompted to restart. During this restart, you should **interrupt at the hardware boot screen** with the BIOS/UEFI key, enter your BIOS password, and make sure to take the live-bootable media back out of the boot priority list.

... You do use a password on your UEFI config, don't you?

## Step 2: Fetch Dependencies, Install Apps
When the reboot is complete, you'll be dropped into the Display Manager to log in - SLIM, if you're using MATE as your Desktop Environment - whereupon you should log in as your main user. As previously stated, you can get a root shell under Applications>System Tools>Root Terminal, whereupon you'll be prompted for root's password.

I handled this step in three main steps:
- edit `/etc/apt/sources.list` to comment out the CD Rom sources - without this step, `apt-get` and its cousin commands will all try to retrieve packages from the install disk, and fail to do so unless you actually had burned that to an optical disk.
- run the obvious `apt-get update && apt-get upgrade && apt-get dist-upgrade` chain. This fetches any packages that have fallen out of date since the last time the Devuan team packaged their installer ISO. For all I know this is covered by the OS install process, but when I ran these commands I did pick up at least one or two new packages.
- Install the rest of the missing environment, like pip3 and my actual applications. This is going to vary for everybody - I was able to get a few tools (such as curl and pip3) via APT, but a lot of what I use required me to first fetch the .deb files  or source from which to install them.

## Step 3: Restore from Transfer
I was actually able to largely just unload my transferred /home/ directory into my new home without many issues, and a lot of what I needed to carry over actually did. I'm still missing a few points of configuration, but certainly not any more than I would have been missing from an otherwise naked install of the OS. This was also a good time to grab Tapestry again using `pip3 install tapestry` (as user), and to take a few minutes and make sure all of my keys were okay.

## Step 4: Resolve Pain Points
While at the end of step 3 the migration was more or less completed, I did identify two particular pain points that required some troubleshooting. I'm still deciding if they're a mark against Devuan or not - if I decide I feel they are I'll make sure to let them know.

### Yubikey Support Out of Box Is... Lacking
As I've written about before, I'm an avid supporter of the use of hardware authentication tokens for a lot of reasons. I use mine for U2F passthrough, OTP authentication, and as an OpenPGP smart key. While OTP authentication worked fine (since it just emulates a keyboard and punches the password in), the other two functions required a little manipulation:
- OpenPGP smart keys - I restored this to working order by calling `apt-get install scdaemon pcscd yubikey-personalization libusb-1.0-0-dev`. I'm not wholly certain these are all needed if the key is already configured - I pulled this off my from-scratch runsheet. Having them present certainly allowed the key to start functioning as designed again.
- U2F Passthrough - This baffled me for a short time, until I remembered that this is a permissions issue. Specifically, you need the following rule in `/etc/udev/rules.d/99-yubikeys.rules` so that the device has the right permissions:
```bash
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1050", ATTRS{idProduct}=="0407", GROUP="plugdev"
```

Again, this is essentially a proprietary product being hamstrung to follow open standards. I'm not wholly certain Debian *should* support it out of the box where adoption is relatively low. There's a lot more that could be done with a Yubikey too - they can be used to authenticate to LUKS, and for PIV to give a passwordless login experience.

### Dual Monitors are Broken As Hell
Well, maybe not for everyone, but it certainly was for me. This box relies on AMD hardware to get its doing done, and for quite a while I struggled to get Devuan to even realize I had a monitor connected to HDMI out, never mind treat it appropriately.

I eventually realized that dmesg is a thing, and puzzled over the output for a few minutes before I found a likely failure candidate - a bit of AMD firmware that was in error -2, which meant it wasn't actually on disk. It took a little more puzzling over that before I learned that, although the xorg package containing AMD drivers was installed, there's a second package, `firmware-amd-graphics` which is not, and that was what contained my missing files.

After a quick install and a reboot, I at least have detection of the second monitor working. Time'll tell how stable it is in terms of being hot-swappable, as I only use the secondary monitor in one specific context. There's also a bit of missing functionality I had under Ubuntu, which is the ability to set the secondary monitor up separately from workspaces. I'm not heartbroken that that's not the default case, and I'm confident that overall, I can probably fix it.

## Overall Key Takeaway: Devuan's Great
In most use cases, you're not going to run into the problems I did. Both were pretty deep into the territory of things you could consider edge cases. Other than that, Devuan's a great, mature fork of a mature lineage of GNU/Linux that beautifully supports the overall "free as in speech" attitude that Free Software is built on.

That said, Devuan is also free-as-in-beer software, provided gratis to users around the world because we all deserve to have this kind of liberty. I'm pretty excited about their level of transparency in terms of how their funds are used, and their willingness to take on new development help as time goes on. I've been thinking of contributing myself, but you can also always donate directly to their work [here](https://www.devuan.org/os/donate) 

If you found this or any other article on the blog helpful, or want to help support Kensho Security Labs as we go through our 2020 Upgrade Process, I'll freely admit that coffee is the ultimate debugging fluid and your donation of a cup to me would be appreciated greatly:
[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/A8614WZ3)