---
layout: post
title:  "Tapestry 0.3.1 Preview!"
date:   2017-09-08 08:30:00 -0300
categories: Tapestry
---

In spite of two gigantic sidequests - some Tarnished Tale work and work on the [Patch Savage Labs Website](https://www.psavlabs.com) - work on Tapestry has continued more or less apace. As expected, there's nothing major coming down the pipe for 0.3.1, but I thought I'd talk about it anyway since the release will be delayed until somewhere around the end of the month.

## Death of Loopback Pinentry
As expected, Loopback Pinentry turned out to be not just a security flaw but also an unnecessary one. A chance discovery by dint of a lucky guess has allowed Tapestry to instead use your default pinentry program when making requests of GPG that would require a passphrase or pin to complete - including during key generation. The code changes that cause this were actually pushed as a releaseless patch to 0.3.0, but since they weren't discussed then, I'm mentioning them now.

The solution, by the way, was ill-documented, but simple. Using the `python-gnupg` module, if the kwarg `secret` is not supplied, GPG will prompt using your pinentry method of choice - gnome in my case, but you might be using something different.

## Minor: Fixed Blockbuild
I once ran a live backup-mode Tapestry execution that generated, for some reason, a second block which contained only the recovery pickle. This was determined to be caused by the process which detects and skips oversized files failing to update the size accordingly. Two-line fix.

## Replacing the FID counter with proper UUIDs
Twitter followers may recall I discovered an interesting bug during 0.3.0 testing which caused files whose filenames collided with an extant FID value to be spontaneously, though reproducably, deleted upon recovery. Obviously this was unacceptable.

To prevent this issue, the FID value has been replaced with a standards-compliant UUID value generated with the standard library's `uuid` module. Hopefully that settles the issue.

## Deprecation of KeyringMode=False and adding --newKey
Longtime followers or users of Tapestry will remember I have made much ado in the past over a `keyringMode` configuration variable which, if false, causes the program to go searching all connected removable media for a key. As of 0.3.1 this functionality is being excised. Operating in this way made neither deployment of Tapestry easier nor made a noticable positive impact on overall security.

Since `findkey()` was formerly the only time the key generation code was called, I have added a new mode flag, --newKey, which will call the key generation code before running whatever other mode was passed to it. Key generation makes a new 2048-bit RSA keypair using some user-supplied information such as userID and passphrase, then sets this new key's fingerprint as `Expected FP` in tapestry.cfg and exports it to the designated drop folder as one public and one private certificate.
