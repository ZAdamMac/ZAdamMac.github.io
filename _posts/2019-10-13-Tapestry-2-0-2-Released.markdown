---
layout: post
title: "Tapestry 2.0.2: The Tester's Rewrite"
date: 2019-10-13 18:30 -000
categories: Tapestry
---

By far the biggest project being worked on right now at Kensho Security Labs is not Tapestry, but Enumpi. But that doesn't mean we've let Tapestry fall completely by the website. We just released [version 2.0.2 of the software](https://pypi.org/project/tapestry/), bringing some admittedly-minor changes to the software that brings you safe, secure, store-anywhere backup functionality. All we did, really, was add a few functional bypasses called when the program is in testing mode to allow bypassing some forced interaction steps.

This release co-incides with merging our [Test Framework Rewrite](https://github.com/zadammac/Tapestry) into the master branch. If you've been thinking about contributing to the Tapestry project, it's now easier than ever - our new test framework allows for near-instant per-commit testing and expedient runtime testing prior to pull request operations, and is set up in such a way that adding new tests to cover new features is a triviality!

We did this by turning the tests into something of a locally-run module which:
- runs a series of positive- and negative-control unit tests against specific functions in the Tapestry suite.
- Optionally includes network functionality tests (so that you don't always have to have an SFTP share exposed!)
- Optionally include full runtime tests (excluding these by default saves up to 40 minutes per testing iteration!)

Adding a new test is as simple as editing either positive_tests or negative_tests to include the new test as a function, and putting that strings for the function in the right json catalogue. Much simpler than the old, ad-hoc method!

Tapestry is free-as-in-beer software, provided gratis to users around the world because we all deserve to have secure, reproducible, private backups on hand. All that being said, if you wanted to contribute to the project financially, your best avenue is via [Github Sponsors](https://github.com/users/ZAdamMac/sponsorship.)! Github are matching sponsorship contributions for the first year of the program, so your donation would go twice as far.

If you were more interested in a one-off donation, you could always buy me a coffee instead:
[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/A8614WZ3)