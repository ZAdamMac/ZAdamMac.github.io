---
layout: post
title: "Y'all MFs Need Backups: Some Evangelism"
date: 2018-01-31 20:30 -400
categories: Infosec
---

## Defense In Depth: It's Redundancy All the Way Down!

It's among the first things we're taught as computer users, an a habit anyone familiar with working routinely on computers is familiar with: save often. What happens, those, when you lose your files? Whether through malice or accident, this is a situation we must consider.

Defense of information systems comes in depth, it's a matter of layers. This is true of both household systems and the sort of interesting machine configurations we see in the enterprise. But when it comes down to it, there is one, final layer. The absolute last line of defense against some attacks and the last resort in some situations.

It's not sexy. It's not fun. But it is absolutely non-negotiable.

## Backups As A Protective Element

Backups protect us from abject failures of the rest of the system - in fact, for very large, critical systems, sometimes backups are literally entire duplicates of the system that operations can switch to on a moment's notice. It doesn't matter if the failure was born in malace - an infection by a drive wiper, ransomware, or a rogue sysadmin - or natural. Having a backup, administered correctly, will save you. Sort of.

I like to think of backups in this way, rather than as a last-resort contingency. Having a backup procedure in place protects you from some of the "absolute worsts". Ransomware attacks have been all the rage lately. Did you see last week when it was revealed that some ransomware payoff pages had been hijacked? Hell of a scam, right? Rips off both the victim (who doesn't get their files back after all) and the attacker (who didn't get paid and therefor wants to be paid again). That's assuming the ransomware you got infected with is even actually reversable! This sort of crisis is easily avoided by having the right model of back-up data to restore from.

Of course, we also have to think about the way in which we store our backups. Some years ago, my brother and I were living together and we were robbed. I had aggressive backups even at the time and I would have been fine - fortune had it that had I restored from backup I'd have lost about six man-hours worth of work. Of course, this backup was stored in a Sentry fireproof lock-box under my bed, which was also stolen. Oops. It's not just theft either. What about a fire, or a flood?

## What to Back Up?

Of course, I'm going to look at these issues - how to defend the backup - in a moment. But first, it's important to talk about what to back up. More accurately, it's essential to help you understand how to answer that question. A blanket prescription isn't going to work for everyone - grandma who only keeps photos and poetry on her email/web-surfing machine can afford to do full-image backups. A major enterprise with a hundred workstations probably can't do that.

The issue is balancing the desired size of the backup while maximizing the convenience of restoring from it. A backup that contains nothing other than your documents, photos, music, and video folders is perfectly acceptable. It's also perfectly exceptable to back up all your system settings folders.

Modern backup systems try to solve this problem by using differential backups - they create some known-good master state and then make successive backups nothing more than the files which changed. This can be good, but it also adds administrative overhead. It's slower in both backup generation and backup recovery. For some applications, you can't afford to wait. 

Tapestry, my personal backup tool project, does whole-file backups, as a fresh run every time. That does put some burden on the user in deciding what is important enough to back up and store, but that also means their backup need only be run once, and their recovery need take no more time than one simple pass instead of a whole chain of changes. That makes for less overhead in managing storage.

## Defending the Backup

Managing storage is only one aspect of protecting your backup, and backing up is pointless if you don't make at least some effort to maintain the backup itself. A backup needs a few qualities. You have  to be able to find it when you need it. You have to be able to trust you retrieved it in the same state as you left it. You have to trust it was generated in a way that was safe to restore from in the first place. And, perhaps most importantly, you need to know that nobody else can investigate it.

At present, most advocate for some form or another of cloud storage of the backup. If you have a reputable cloud storage provider and have taken reasonable steps to secure your account, that solves all but one of these problems - that of the original backup having integrity in the first place. 

I am professionally paranoid, of course - in my previous post I talked about eliminating trust from Tapestry's operating model. This is an area in which this is useful. It still doesn't solve the integrity problem, but it removes your reliance on trusting your cloud provider to keep other people out of your backup, not to modify the backup themselves. You need only trust that your backup would be retrievable.

I personally do not recommend the backup stored in the same physical location as the original. This does offer attractive cost and security benefits, but it fails to protect against catastrophic loss. It's still better than nothing, though, so if you have no other means to store your backups, grab a network-connected drive or a USB HDD and do that up.

### Backup Integrity

It's important to ensure the backup has integrity - that the files inside the backup are present, correct, and not malicious or otherwise modified.

This is achieved pretty simply by forcing an antivirus scan with a modern, up-to-date definitions package against the directories or files you wish to back up. There are a few mechanisms to achieve this, but in general, the simplest way is to schedule your antivirus to run a full scan immiediately before your scheduled backup.

## Frequency of Backup

But how frequently should you schedule those backups? Again, this depends heavily on use-case. If the device you are using is updated rarely or used only on occasion - perhaps some sort of dedicated provisioning box, you won't want to back it up as frequently as you would a daily-use workstation.

There's also a question to answer when it comes to content-differential backups such as the kind produced by Tapestry, which has two modes - Default and Inclusive. The Inclusive mode is, by design, going to produce larger backups than the Default, to allow you to back up frequently-changing directories more frequently than more static ones.

My personal system is to run Tapestry monthly, and take the inclusive backup every third month. Of course, YMMV, and you may wish to do defaults more frequently.

## What to look forward to with Tapestry Backup Tool

We've been talking a lot about Tapestry in today's post, which might seem odd for a project that was supposed to be in maintenance mode. As you might have guessed, I'm pulling it back into active production.

Currently, we're awaiting Tapestry 1.0.0, the first long-term release. It will come out as close to immediately after the release of the updated `python-gnupg` module as possible, as it depends on a bugfix from that program.

Here follows a list of the features added to 1.0.0 when compared to the current 0.3.X series of releases:
- Added Functional Testing for this and All Future Builds; no more hoping I don't miss anything.
- Re-implements the BZ2 compression process within certain considerations and sane defaults.
- Implements a special --genKey flag that generates and exports a new key when called, before executing the rest of its arguments.
- Switches the current FID system to UUID-based systems. This does not break past backups, but does avoid a rather serious bug which resulted in smashed backup records under certain circumstances, at a small penalty in initial performance and final backup size.
- Improved documentation and default configurations to be packaged with the release version.