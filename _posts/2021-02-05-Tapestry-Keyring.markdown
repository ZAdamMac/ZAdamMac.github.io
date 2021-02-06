---
layout: post
title: "Adding System Keyring Functionality to Tapestry"
date: 2021-02-05 22:15 -400
categories: tapestry
---

Recently - this very morning, in fact - it was suggested that I move credential storage for Tapestry out of the "no, you're not allowed" space and into the local system keyring by use of the [python library module of the same name](https://pypi.org/project/keyring/). After a quick review I decided I would, and the current version of the development code for Tapestry already includes the necessary changes. I did however want to talk about why this decision was made and why you may or may not want to use it.

## Why the new dependency?
By allowing us to interface with the system store, `keyring` gives us a mechanism to store passwords that is marginally more secure than storing the passwords in the `tapestry.cfg` primary configuration file, which up until now was forbidden. However, not having any means to store the tapestry credentialling information meant that certain functions like block signing or network storage were diffuclt to safely automate and required some degree or another of user intervention to handle securely.

### So What's the Problem?
Keyring access is handled at the level of the running binary. This means that any script running within python potentially has access to the secrets stored in Tapestry. This puts a hard upper bound on what values should ever be exposed for the user to potentially store - the passphrase for the Disaster Recovery key, for example, should never be storable as there is no sane reason to do so, as well as a soft limit where the user needs to make their own personal risk assessment decisions.

For the average user with an average threat model, the bottom line is that the change will make using tapestry in an automated way more convenient with a minimal risk of exposure to sensitive credentials. At worst, under normal usage, the storage might be compromised - but the design goal of tapestry has always been to explicitly distrust that the storage location itself is secure, and instead to count on its reliable availability only.

---

Tapestry 2.2, including this feature, small quality of life changes, and automated handling of backup storage in Amazon S3, is coming in March or April 2021. If you would like to support the development of this free, zero-trust backup utility, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac) or by making a one-time donation to [Kensho Security Labs via Ko-Fi.com](https://ko-fi.com/KenshoSec) or through [other avenues detailed here](https://www.kenshosec.com/support.html). The development of S3 storage in a sane and reliable way will incur an ongoing per-test maintenance cost to the labs which is not currently offset apart from by your support and donations.