---
layout: post
title:  "Sneak Peak at Tapestry 0.3.0!"
date:   2017-06-10 12:40:00 -0300
categories: Tapestry
---

[Tapestry's](https://github.com/ZAdamMac/Patchs-Tapestry) next release was intended to be a small set of fixes, finally fixing the passphrase support for Tapestry-generated PGP keys in both the generator and the recovery modes, and a few related small cleaning changes to the way it outputs files. However, a small change in my work environment (and the discovery that somehow my laptop is suitable for hosting a Win7 VM) meant that what was to be 0.2.3 is instead going to be 0.3.0!

Cross-platform portability had always been a part of Tapestry's design from the very beginning - it was intended as a fairly specialized tool for running backups on all of the computers I had admin access to. However, the lack of ready access to windows (when my gaming rig, Gliesse, ate itself) and the relatively low priority of development in OS X meant that the feature kind of took the back burner.

For the most part, the process of copying, arranging, and encrypting files is pretty much the same on every OS python services. However, there are a few difference between Windows and your various UNIX/Linux flavours that made adding the windows features back in pleasantly non-trivial compared to most of the improvements I've been making.

## findKey() and findBlocks()
If keyring mode is disabled (which is the default), Tapestry begins its run by hunting for a valid PGP key - either DRPub.key in the default mode or DR.key in `--rcv` mode. It is assumed this key will be located on removable media, as this is the intended use. Searching across recoverable media under UNIX/Linux is easy and straightforward and is done with `os.walk(/media/)`, owing to how the OS handles media. Conveniently, this also works for looking for .tap blocks to recover from.

However, windows treats removable media differently, owing to the fact it treats all drive volumes differently. Finding one USB key is somewhat more complicated - an iterator has to figure out which of the many drives after C:/  are mounted, and then go searching for the file. I anticipate this process to be rather slow if multiple HDDs are mounted under the file system.

Fortunately, windows itself provides something of the answer. It is possible to designate a specific USB drive to always mount with a specific drive letter - provided that letter is available. The `Environment Variables` section of the tapestry config file will include a "check first" option, the intended use case being to set this to the value of the drive you store recovery keys on. Of course, you'll also be able to simply use keyring mode and just skip all that tedium. This might be a more viable option in large networks anyway - moving around a flash drive from system to system adds tedium and a potential malware vector.

A config variable also exists to define the drive letter of the optical disk drive - assuming you are recovering from disks - which will default to `D:/`.

## Other Fixes
As mentioned, there's a variety of small fixes coming up as well. Most importantly is new parameters to support passphrases on the recovery key - a feature previously only seen on the signing key. Just as a signing key passphrase provides assurance that the person to whom the key belongs actually signed it, adding a passphrase to the recovery key still allows virtually anyone - even a script - to create Tapestry backups while ensuring only an authorized individual can recover from it. I feel this feature really does support the use of the keyring mode and strongly recommend you genereate new recovery keys or add passphrase protection to your existing key. Just remember to keep your old keys if you still have older tapestry backups in your system that you might need to recover. :)
