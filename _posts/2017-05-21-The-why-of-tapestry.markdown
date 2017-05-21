---
layout: post
title:  "The Why of Tapestry"
date:   2017-05-21 12:40:06 -0300
categories: Tapestry Design
---

[Tapestry](https://github.com/ZAdamMac/Patchs-Tapestry) has a very odd design. Not odd in that it is novel (I'm almost certain it isn't) or odd in its extreme pecularity, but odd in that it is oddly specific. A traditional backup tool functions not unlike a drive snapshot. It pulls your files, and often your applications and OS settings, every time it is run. Contrast with Tapestry, which by default only grabs your Documents and Photos - Music and Videos, too, if run inclusively.

Nothing is preventing you from defining the root of the file system as a category and cloning an entire drive. But why grab specific areas, under specific categories, and package them up in more-or-less equally-sized portions? And why so much crypto on top?

To start with, let's look at the hideous ball of spaghetti that is the `buildBlocks()`, which does the following:

- Make a new directory in temp
- Pack files into that directory in a particular way until the directory is full
- Repeat until the list of files has been expended

## Why Blocks?
As stated previously, Tapestry generates backups in the form of blocks of a given size, provided by that instance's `Tapestry.cfg` file. This was because of the original manner in which I was backing up media. I envisioned backups to physical, write-once media (originally DVD-Rs, but I intend to migrate to BD-R eventually), which could then be stored offsite easily and cheaply as part of my daily routine. So, not only was ensuring that the output files were small enough to fit on a disk important, I also needed to create the minimum number, thus the sorting found in  `buildSlocks()`, here simplified:

- Grab the first remaining item in the dictionary `listSizes` which was sorted by size
- Check if it fits
  - Doesn't fit, and won't ever fit: Logged and removed from the list
  - Doesn't fit on this pass: skipped
- Copy it to the current block-directory
- Repeat until clear

This function is not unlike packing physical items into a limited space, like furniture on the back of a truck. You place your largest items first, and then fill the gap with the smallest. If the Documents directory/library is part of your backup, you probably have plenty of small, sub-MB files to use as padding. Similarly, if some of your files are excessively large (say, whole OS isos.), they will be skipped forever and you can back them up seperately.

It's important to note that burning to disk might not be the only reason you'd want to use blocks. It could be helpful when moving the files to an offsite backup digitally as well, or even when copying to an external HDD or SSD. Suppose the connection was faulty or otherwise interrupted. At most, you should only have to retransmit the present block and any subsequent blocks.

## Why whole files?
Partly, this was due to squeemishness on my part. It IS possible to convert the entire backup list into a single file, simply truncate it at the right size bounds, and create the absolute minimum number of required disks that way. However, I wasn't comfortable with my ability to do that.

On the other hand, there is an advantage to using whole files, and that advantage is in the files themselves having been aggressively indexed on the storage media. in `foo-date-1.tap`, additional room was set aside for a recovery "pickle", which contains all the information Tapestry needs to recover all the blocks in the set. This includes the original filenames.

A proposed future feature - one I might one day implement, but not at priority - is the ability to request a specific file from the backup. One would simply insert disk 1 to get the index, and then Tapestry would tell you which disk to insert to recover it. This is slightly sub-optimal presently as it would require a change to the format of the recovery pickle, which lately has had a "breaking" effect on backwards compatibility.

### But all my files have been renamed!
Yes. If you look inside a .tap file after it's been decrypted (yielding a .tar.bz2 file), you'll notice all the files besides the recovery pickle are numbered serially. These new filenames are the index number of the item, which is used to figure out precisely where it goes and what it was called. I chose this convention to prevent name collision problems - say you had two copies of `bar.txt`, Tapestry would not otherwise know which was which.

### Why flatten the heirarchy then?
You'd also notice that all the files are in one directory - the root directory of the archive. That's because of two of the dictionaries contained in the recovery pickle. One gives the category in which the item was originally found, the other gives the path from the base of that directory to whatever subdirectory it was originally found in.

When I designed Tapestry I was operating two computers, running two operating systems, each organized slightly differently. I wanted to make sure that, in the event of hardware failure, I could recover the Desktop backup (Win10) to the Laptop (Ubuntu 16-and-change) with minimal loss of that organization. Documents still go to documents, and so on.

Since v0.2.2, items without their category defined in the local `Tapestry.cfg` go to subdirectories on the Desktop.

## Encryption: Why RSA, and why bother?
I chose to use RSA encryption for this project because it's part of the Pretty Good Privacy standard, and an implmentation of PGP (Gnu Privacy Guard, or GPG) was already available across all the platforms I intended to use it on. I was going to be storing these disks somewhere I didn't have constant overwatch of them. The use of single-write disks may have prevented people from altering them, but if I wanted to prevent unauthorized reads, I was going to have to encrypt them in one way or another.

### Isn't assymetric key encryption slow on large files?
Yes, which is why the PGP standard doesn't directly encrypt the files assymetrically. The GPG manual has a good explanation of exactly how this works, but using GPG to encrypt the blocks still achieved one goal - reducing the number of passwords necessary for the user.

### Is that why the Disaster Recovery Key doesn't use a passphrase?
Yes. My original design for the program called for a purpose-specific disaster-recovery key, which was kept off of the main keyring on its own removable media (a thumb drive, in my case). In this model, one user used one key that they kept physically secure by having the drive with them at all times. A passphrase under those circumstances seemed needlessly complicated

Of course, this may change in future versions, see below.

### In that case, why allow the "Keyring Mode" at all?
A devolution aimed at automation, I'm afraid. It suddenly occured to me that an organization could use tapestry - several client computers all running tapestry backups which could then be recovered by a single operator - the system administrator or some similar title. It made sense then to allow Keyring Mode, which can call the key off the keyring (which might only be the public half anyway).

In this use case, passphrase protection of the Disaster Recovery key starts to make a bit more sense, so I am looking into adding it to future releases. Passphrase handling for the signing key is already supported as of v0.2.1.

### Why support passphrases for the signing key but not the recovery key?
As stated, I do intend to support both, but it was a more pressing issue for the signing key. A signing key should be specific where-ever possible to one person, which is the person drawing the backups. I, for one, like something that can be used to more-or-less conclusively sign my name to be secure, and therefore I have a rather overbearing scheme for my personal RSA key - a good topic for a future post. I needed passphrase handling for that.

**It is important to note that this particular mechanism of passphrase handling is not secure.** Python-GNUPG simply pipes the passphrase to the gpg-agent over the loopback interface. Presumably this is highly sniffable - I know I've monitored traffic on the loopback interface for less malicious reasons. However, I generally consider it to be important to only generate backups from systems in a known-clean state. Future versions may attempt to mitigate this by bypassing Python-GNUPG and calling into GPG directly, but that would involve essentially completely re-implementing the module from scratch.

Moreover, newer versions of GPG actually forbid this behaviour by default, and require a modification to the relevant config files to allow it. If you're concerned about this, you should disable automatic signing and instead sign your blocks manually.

### Why sign the blocks at all?
The snarky answer is paranoia. On explaining the principle to a co-worker (I store the offsite in our office at work, and the offsite of the work computer in my office at home), he remarked that the disks could easily be exchanged, particularly if the DR Key was somehow compromised.

Signing provides a level of tamper-resistance. Even if the key was compromised or the disk somehow otherwise tampered with, it's possible to verify the signature of the block against a second, hopefully uncompromised, key.

### Why allow the user to restore blocks that have insufficiently valid signatures?
It might be necessary - say signing is disabled by default and not part of your security model, or you know J Random Op generated the disk but their key is inadequately trusted on your keyring (perhaps because you are recovering from a re-image and haven't re-verified all your keys yet), you may want to get your files back more than you want to make sure you haven't been mossaded.

Because you are going to be mossaded, no matter how careful you are.

##Therefore:
That's about the gist of it all. Further questions should be directed to one of the many fine points of contact below, until I have the time and wherewithall to enable comments here.
