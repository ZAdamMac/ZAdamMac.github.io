----
layout: post
title: "Tapestry 2.0 Is Now Available!"
date: 2019-04-16 18:00 -400
categories: Tapestry
----

Tapestry 2.0 was released this afternoon. I wanted to throw together a quick blog post about it so that I could plug the new features, explain the major changes, and drop some hints about what we might see in future releases (and when we might expect those). A future blog post is going to offer a retrospective on the 2.0.0 rewrite as a project in its own right!

[Tapestry](https://kenshosec.com/Projects/tapestry.html) is a Free, Open-Source Software tool Kensho Security Labs is developing to automate the process of making Zero Storage Trust Backup records, and recovering from those records. We're currently looking for contributors, so check out [the github repo](https://github.com/ZAdamMac/Tapestry).

# What's New In 2.0?
Technically speaking, everything. Tapestry 2.0 is a from-scratch rebuild of the entire project. We've broken the monolithic script into a full python package, which will be installable using the standard python package manager, `pip`. This will make the script easier to use, both for the developers and for end users.

## Reintroducing: Windows Support
Up until the 0.3.0 multiprocessing rewrite, Tapestry had been designed - and intended for use - fully independent of the host OS. While 0.3.0 broke support for Windows, the full rewrite of Tapestry was the perfect opportunity to change our handling of the multiprocessing operation in order to allow it to function correctly on both Unix and Windows environments.

## NewRIFF: Improved Index Format
Prior to Tapestry 2.0, Tapestry has only been able to track its recovery indexes using python's standard 'pickle' library, and the format in which that data was stored using pickle made it hard to add many new functionalities to Tapestry. As a result, one of the objectives of the 2.0 rewrite was also to improve the Index.

Tapestry now relies on NewRIFF, a standardized JSON recovery index designed for the purpose. NewRIFF is designed in such a way that future extensibility is a cornerstone, and implements support for many of the new features planned for future releases in the Tapestry 2 series, such as integrity checking and support for Loom.

What's more, we've changed Tapestry to add the Index to *every* block in a given run. If one block becomes corrupted, or the blocks are fed to Tapestry out of order, there's no longer any reason to mess about with finding the index - as long as one readable copy exists, your files will be fully recoverable.

Don't worry, though. Unlike the breaking 0.2.0 release, Tapestry 2.0 will retain support for the old recovery-index file. While new backups created will use NewRIFF exclusively, Tapestry will still be able to recover your older backups without issue!

## Platform-Independent Recovery
Tapestry's default configuration file lets you define category paths for both windows and Linux. This is by design - a Tapestry backup can be recovered to either, entirely irrespective of which OS was used to generate the files in the first place!

## Improved CLI
Like many python projects, Tapestry lives in the terminal. That doesn't mean it has to be hideous, or difficult for a human reading its output to understand. Tapestry now has improved status and error messages, and, critically, an improved progress bar system.

# Why Install As a Module?
As mentioned, Tapestry 2.0 will be available from either the github repo itself for immediate "compiling" and install via `pip`.

## User Convenience
Currently, Tapestry requires you to either remember where it is (and then CD to its location), or add it manually to the $PATH. This is a little inconvenient. Instead of having to invoke:
```commandline
cd $TAPESTRYDIR
python tapestry-1.0.1.py
```

From now on, users will simply have to tell their terminal to:
```commandline
python -m tapestry --inc -c tapestry.cfg
```

## Improved Code Structure
Packaging as a module was actually made necessary by changes to the implementation of Tapestry itself. The 2.0 release is a complete rewrite, and one of the biggest changes was the decision to break off the objects used by Tapestry into their own file, `classes.py`. Useful for streamlining future development, this change made it necessary to package Tapestry as a module in order to have the test framework work on it accurately, so the decision was a net win all around.

# What's Next for Tapestry?
For the time being, the 2.0 series is going to consist entirely of bug-fix releases and security updates, as necessary, while work on other projects takes priority.

However there are some good features still in the pipe which will make up future updates.

## Improved Test Coverage and Utility
A future release to the repo will be an enhanced testing suite leveraging CI and increased unit testing over functional testing to streamline new feature development.

## SFTP Support
The current network storage options for Tapestry, Network Share and FTP with TLS, turned out to be rarer "in the wild" than we originally anticipated. Due to that experience, we intend to add support for FTP over SSH (SFTP) in a future release, using both the username/passphrase and authentication key methods.

## Loom Support
The idea of the Loom Project - a centralized architecture for managing, distributing, and recovering from Tapestry archives in a secure and tidy way, has not died. While developing Loom has not currently been a priority, NewRIFF solved, in advance, many of the problems implementing Loom would eventually face, as will the SFTP release. Rework of Tapestry to support Loom would no longer require the same level of work as it did prior to Tapestry 2.0, and developing Loom is accordingly taking more of a priority position at the Labs.

## Runtime and On-Demand File Integrity Checks
NewRiff added a section to the entry for each file used to store a checksum of that file's contents. This was not purely theater. A feature is in the works to allow for checking of the after-packaging version of the file against that stored value, either while building the backup archive itself, or on demand, as long as the user has the relevant key material.

Our feeling is that this is an important feature in any backup utility, and so adding in these checks is a priority project, expected for release in summer or fall of 2019.

# How you can help?
If you like Tapestry and the direction it's heading from, we'd like to hear from you. Unlike many other forms of software, Tapestry currently has absolutely no telemetry of any kind built in. We're not even sure how many other people have used it!

If you have suggestions for or problems with Tapestry, please let us know at [the Tapestry inbox](mailto:tapestry@kenshosec.com). If you're also a developer, particularly in Python, feel free to check out [the github repo](https://github.com/zadammac/Tapestry), submit a PR, answer questions, or even just give your thoughts and feedback.

We've kept the cost of Tapestry as low as possible, and I refuse to make the script itself nagware, or change the license to require payment. That said, if you wanted to contribute to the project financially, our best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).

Follow my twitter, [@zadammac](https://twitter.com/zadammac) for the upcoming announcement of the associated mailing list for announcements regarding Tapestry, including update releases.
