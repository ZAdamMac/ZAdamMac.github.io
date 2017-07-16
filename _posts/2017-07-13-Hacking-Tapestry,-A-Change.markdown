---
layout: post
title:  "Hacking Tapestry: Some Changes"
date:   2017-07-16 00:40:00 -0300
categories: Tapestry SoftDesigns
---

You might remember that over one month ago, I announced that [Tapestry](https://github.com/ZAdamMac/Patchs-Tapestry) v.0.3.0 was going to have a whole bunch of new features included in it that weren't there before, and I am happy to say that all of those features work, for versions of work. So where the hell is it? I did a bad.

While I was banging together support for Windows-style drive-lettering and improving security by allowing the recovery key itself to be passphrase-protected, I realized during repeated tests that Tapestry is slow. Granted, I wasn't expecting any backup tool to operate at blazing fast speeds - it's an overnight cron job anyway - but on the order of ten to fifteen minutes for a single gig of data didn't seem quite what I wanted. It didn't seem what future users might want either, so I decided to change it.

Those of you who follow me on twitter probably remember seeing me spam your timelines about some tests I was running, and it turns out I was right - if I leveraged python-multiprocessing I could take advantage of modern computing tech to reduce the handle time - on some steps as much as 30%! This lead to an absolute redesign of Tapestry.

## No More buildBlocks()
Granted, the buildBlocks function is still called that, but now it works differently. Instead of copying all the files to be backed up into special temporary directories for later functions to work on, now Tapestry simply populates a bunch of tarBlock classes that contain the contents of each block. The general blockbuilding algorithm is still the same, so size-wise, Tapestry hasn't gotten any more efficient. But by skipping the copying step we are both removing a goodly chunk of the build process (about four minutes/gig on the Slaptop) and avoiding using any more space-on-disk than absolutely necessary, which can be useful for systems that are pushing their storage capacities already.

## Multithreaded Compression and Encryption
So, every time a file was added to a tarblock object, another object was enqueued which would tell a process assigned to it to either add that file to a tar (these *are* stored in /tmp/), or to pass the whole tar to the crypto engine. All phases of the building process after buildblocks() are intended to be used in a multithreaded context.

The current implementation is to do everything up to and including buildblocks in the parent process, then spawn processes equal to the number of available cores, and have those processes iterate over the queue. Unfortunately, this doesn't quite work as yet - at the moment, each process tries to grab the config file, which it can't do, and then crashes. I'm exploring ways around this, including to moving on to the concept of a manager process and worker pools in case part of the problem is down to the config object not being pickleable.

## Recovery Changes
Recovery is also getting revamped. Instead of unpacking the whole of each block to the drive, some objects are created that allow what is esentially reverse build - each file in sequence is grabbed by a worker process, extracted to where it was meant to go, and then renamed to its original name and file extension. This was done to reduce the tempfile overhead.

An alternate possibility - maybe for a later version as a recovery option - would be to extract all the blocks from their disks into temp without actually unpacking anything, and then iterate over the entire backup at once to unpack it. Unlike the method above, this would reduce the window of time that human intervention in unpacking is necessary.

## So, where is it?
Well, as I said in the section on Multithreaded Compression, I haven't quite worked out all the bugs, so no release yet. Multiprocessing and Multithreading are new concepts that I haven't used before now, and hacking on them is very much a grinding process, changing as little as possible on each pass until the code works.  Even once I have a working version, I'll still have to test it against my Windows VM,and it's highly likely that I'll continue to refine it in subsequent updates.

I can't stress enough that all subversions of v0 in *any* project of mine are **not** consumer-ready. Hell, even the hackish should use them at their own risk. I'm not even sure I'm actually developing a product at this point. I certainly use Tapestry in my monthly "Digital Housekeeping" taskset, but it's a bespoke project built around my own use cases and generalized to others only as an object lesson.

All in all though, it has been and continues to be an excellent exercise for learning Python (specifically) and computer science principles (generally) for a poor larval-stage hacker who learns basically only by doing.
