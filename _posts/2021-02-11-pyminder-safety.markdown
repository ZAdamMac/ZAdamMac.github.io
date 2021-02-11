---
layout: post
title: "Pyminder's Unsecure Design"
date: 2021-02-11 16:00 -400
categories: security pyminder
---

People who follow along on social media might have noticed that I have been [livestreaming a software project](https://twitch.tv/patchsavage) I'm working on that involves using a Raspberry Pi 3A+ and a [Pimoroni GFX Hat](https://shop.pimoroni.com/products/gfx-hat) to let me, effectively, run cron jobs that print messages to the LCD based on their results. I've been doing so using the popular python module Flask to expose a RESTful API, which isn't exactly secured in the current implementation, and I've been doing that for a very particular reason: this is a local-host only service on a lab-facing device.

While I ultimately intend to prepare a hardened version of Pyminder that would be suitable for exposure as an actual network service, it's not a priority, and not needed for the intended use case of the device. The cron jobs that do the various status and health checks will be running on the pi itself and only need to communicate over localhost. Additionally, most of these status checks will be targeting endpoints on the local LAN.

For this to be a "true" status display/dashboard offering, it would be necessary to further harden the service, by enabling HTTPS on the pyminder service itself, and changing the authentication method from the current passed-around shared-secret model to something like OAUTH 2. Additionally, significantly more work than the current passive injection sanitization methods would be needed to truly harden the application itself.

While this is all possible, it's not really needed for the intended use case of the device. If sufficient external interest were shown in the project and in that particular usecase - as a shared network-wide message aggregator and display service, then it would be worth pursuing. But for now, as a lab-only device, the current design should suffice. However, this is not intended as a replacement for something more robust like NewRelic, which is a better utility for network-wide visibility and alerting.

---

Pyminder is one of the many free, open-source projects at Kensho Security Labs. If you would like to support the development of this rudimentary status display service, or any of the other projects I'm working on for Kensho Security Labs, and you wanted to show your support financially, your best avenue is via [my Github Sponsors account](https://github.com/sponsors/ZAdamMac) or by making a one-time donation to [Kensho Security Labs via Ko-Fi.com](https://ko-fi.com/KenshoSec) or through [other avenues detailed here](https://www.kenshosec.com/support.html).
