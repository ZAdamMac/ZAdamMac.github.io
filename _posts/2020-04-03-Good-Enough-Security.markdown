---
layout: post
title: "Good-Enough Security"
date: 2020-04-03 08:30 -300
categories: Security General
---

The recent changes to the way we socialize, as we all helpfully isolate ourselves in order to combat the spread of SARS-Cov-2 and the COVID-19 it spreads, have forced a lot of us into being much more "online" than we typically were before. For many, the most obvious change has been the adoption of personal teleconferencing through platforms like Zoom, Skype, and so-forth.

I've been reading a lot lately on social media about the travesty of the privacy violations and security shortcomings of products like zoom. Even the CBC - Canada's national news broadcaster - has been running sensationalized articles about Zoom in particular, and a practice now known as "Zoom Bombing" - obtaining or fuzzing the meeting IDs for valid zoom sessions and appearing uninvited. What was more, Zoom did not perform any sort of link sanitization in chat (an issue since fixed in a patch released just today), leading to a sensationalized idea that it sort of, kind of, was almost a security vulnerability. While serious voices in the Security Community at large took a sober understanding of the nature of a UNS link (allowing, with OS warnings presented, the link to run an arbitrary program that was already on the target's machine), many viewed this as some kind of major security flaw, and called for the death of the platform.

Now, I started my forray into security like many - as a hardline security and privacy purist. I'm still a privacy purist, and I still don't like the amount of telemetry present in these products. However, I do have to say, from a *security* perspective, purism and alarmist statements serve nobody. For the vast, overwhelming majority of us, the genuine security shortcomings in Zoom are not in our threat model.

Let me explain: what would be done to target the developer of the latest internet cryptocoin and what would be done to target Grandma Doris who just wants to talk to Little Zelda and Cousin Savino are **not the same thing**.

Yes. Zoom (and similar services) lack qualities that people like me prize - such as end-to-end encryption, where the only people with the cryptographic keys to decode the message and video are me and the people I'm talking to. But they also have important features that, when used correctly, can make them fundamentally safe-to-use for ordinary people who are just trying to keep up with their friends and family.

And fundamentally, that's what's important. "Good Enough" Security for the rest of us. Yes, it  would be nice if every tool and every platform were as secure as humanly possible. But we don't need to abstain from the internet until that goal is achieved, because, quite frankly, it never will be. We can only model for threats that are appropriate, and for most people, Zoom and similar systems are Good Enough.

---
If you find any of my projects useful, they can be sponsored by backing ZAdamMac in the [GitHub Sponsors Program](https://github.com/sponsors/ZAdamMac). For the first year of the program, Github is matching your sponsorship pledges - so they essentially count for double!