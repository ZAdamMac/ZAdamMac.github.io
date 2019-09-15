---
layout: post
title: "Tapestry, The Future, and Simplifying"
date: 2019-09-15 19:00 -000
categories: Tapestry
---

Of the various projects underway at Kensho Security Labs, [Tapestry](https://www.kenshosec.com/Projects/tapestry.html) is probably both the flagship project and the most mature codebase. To date, the project has seen some 14 releases across 471 commits on master. 

To be quite fair, the codebase is a mess, and the documentation could be better - that's why [the current project](https://www.github.com/zadammac/Tapestry/projects/5) includes improvements to both the testing framework and the documentation corpus. It's important in Open Software that things be comprehensible and verifiable.

Most of the other previously-announced features for the future **2.1 Release** are going to continue as planned, once the test framework is in place. That means a more efficient use of multiprocessing during file creation under Windows, backup file integrity checking, and the ability to search through a backup to recover a given subset of the files, along with any other small enhancements and the usual bugfixes.

What that won't mean, going forward, is any changes to the networking protocol selection. Over a year ago, I added FTP/S support to Tapestry, assuming a use case that never really manifested. I don't use it, and I'm not sure anyone else does either. For end users who need a different networking protocol, it would actually be relatively trivial to add a second automated task to pick the output files up and drop them off as needed.

This isn't to say that the ultimate goal - a centralized management tool (sometimes called "loom") to use to collect, store, and catalogue Tapestry backups - isn't going to come to fruition. But SFTP support is being removed from the 2.1 sprint - at least until someone says they need it.

The important thing, in my view, is to make Tapestry as *useable* as possible, rather than as *useful* as possible. A clean codebase with plenty of documentation (for users, administrators, and developers) and some clear use guides would do far more to help with that goal.

Besides, *not* developing such a feature will free up time for other projects.

Tapestry is free-as-in-beer software, provided gratis to users around the world because we all deserve to have secure, reproducible, private backups on hand. All that being said, if you wanted to contribute to the project financially, your best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).