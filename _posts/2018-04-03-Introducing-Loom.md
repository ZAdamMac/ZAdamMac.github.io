---
layout: post
title: "Introducing Loom"
date: 2018-03-03 20:00 -400
categories: Tapestry
---

# Tapestry is a Fantastic Tool But It's Missing a Major Feature

With [Tapestry v1.0.1](https://www.github.com/ZAdamMac/Patchs-Tapestry) as the new stable release, tradition demands I announce, in advance, what will be included in Tapestry 1.1.0. What new feature is coming? What new trick am I going to demonstrate I learned?

The answer is the same: **Network Connected Storage**. Tapestry needs this important feature to fit modern use cases. It's one thing to set up the system to automatically burn its output blocks to removable media and then physically ship them offsite. It's another thing entirely to do that sort of thing over the network, and unfortunately, that's most people's current use case.

To be clear, where Tapestry is mostly supported on Unix at the moment, this is already possible by mounting the remote storage location to the filesystem and then defining that as the output path. But that's inelegant, creates a system dependancy of a sort, and isn't the usual way most people deal with network connected storage, privately or otherwise.

# Introducing Loom
Tapestry 1.1 is being designed to support a co-developed service called **Loom**. Loom is essentially a security-managed FTP service with python wrapped around it, in order to provide a few key features.

Loom will be providing the following features over the course of its development:
- Organization by Client and Machine
- Backup metadata administration
- Double Verification of both the Client and the Loom Instance
- Secure two-way file transfer
- Set-and-Forget functionality