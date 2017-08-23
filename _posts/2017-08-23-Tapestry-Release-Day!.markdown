---
layout: post
title:  "Tapestry 0.3.0 is now Live!"
date:   2017-08-23 11:00:00 -0300
categories: Tapestry
---

[Tapestry](https://github.com/ZAdamMac/Patchs-Tapestry) v.0.3.0 has finally gone live! It should come as no surprise that this release doesn't fully support windows, and this much is indicated both by the program itself if you attempt to run it in windows, and the readme itself.

But wait, you say, upon reviewing the source! There's still windows-functionality code in there! Isn't this bloat?

Yes. It is bloat. I still intend to release windows functionality for Tapestry, I'm just changing its place in the priority queue. Windows support is now the roadmap for the next Breaking Change, v 0.4.0. In the meantime, there are several smaller changes that can be made in the 0.3.x series which, importantly, don't require access to my windows VM for testing and development.

## Is this update breaking?
I'm actually not sure, as I didn't immediately test that. 0.3.0 made no changes to the structure of the recovery-pkl file, but I believe the name actually did change, so yes, recovery is broken. If you are still using older backups, you basically have two options:
 - Do a new `--inc` run with 0.3.0 and replace your old backups
 - Retain a copy of 0.2.4.

Of course, old releases are still available from the repo, so you can always check there.

## What's next?
The next Tapestry update, 0.3.1, will improve its handling of the pinentry process under GPG, possibly by hooking gpg-agent's configured pinentry program. I'm looking into it.

## Next Time at the PSLabs Blog
An introduction to another project, a long-term side project I'm codenaming, for now, as Project CLAIR.
