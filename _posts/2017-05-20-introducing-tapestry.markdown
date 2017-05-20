---
layout: post
title:  "Introducing Tapestry!"
date:   2017-05-20 12:40:06 -0300
categories: Tapestry SoftDesigns
---

[Tapestry](https://github.com/ZAdamMac/Patchs-Tapestry) is my specialized backup automation tool, currently in version 0.2.2. The project started as a simple exercise in automating what was, for me, a rather complex task. Naturally, it has since sprawled considerably as an exercise in generalizing its use. It is presently written in Python 2.7.12 with dependencies on Python-GNUPG and Gnu Privacy Guard.

Tapestry works by searching a pre-defined set of directories (and all of their subdirectories), indexing their files, which it then seperates into the minimum possible number of blocks of a user-defined size, before copying, packaging them as a TAR with bz2 compression, and encrypting them with a purpose specific Disaster Recovery RSA key. The intended use case (mine) was to burn the individual disks to single-write media (in my case, DVDs and, later, BD-R) and recover them as necessary, possibly with off-site storage.

Tapestry has a number of features developed since then:

  - Multiplatform Support
  - Automatic Signing/Validation of Backup Files
  - Simple/Extended Backup Location Lists
  - Automatic DR Key generation
  - Keyring-Only Mode
  - Key Change Detection (to prevent fraud)

Intended future features include:

  - Completed Windows and OSX use testing
  - Enhanced key generation, support for passphrases on DR Key
  - GUI/Installable
  - Increased granularity on the inclusive backup function (e.g. Specifically include the category "music")
  - Backup-to-server Function
  - Speed Optimization 
  
In future posts, we'll explore why some of these design decisions were made.