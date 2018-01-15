---
layout: post
title: "Your Face is Not A Secret: Why Biometrics aren't Authentication Tokens"
date: 2018-01-13 11:30 -400
categories: Security Technology
---

Biometrics as a security technology has been in the public eye for some time now (at least since my childhood in the 90s), but it's back in vogue now with the advent of new technologies, such as facial recognition, which were recently widely adopted by Apple in their new iOS devices, and less recently by Android developers around four or five years ago, when it started showing up on their devices. It's sort of the epitome of security technology. We can do away with cumbersome password-based authentication because no two people have the same face/fingerprints/DNA/whatever.

Of course, anyone in the technology sphere knows that no good idea ever survives implementation, and implementations of biometric systems have been famously bad. iPhone X's FaceID system pretty much only worked properly for white folks and could still be authenticated against the faces of siblings. Deviant Ollam gave a talk a few years ago on insecurity in gun safe design where he performed a live demo of popping fingerprint locks using counterfit fingers.

Really, we have two problems here: the implementation problem, and then a deeper, conceptual problem. I'm going to go over each in turn.

# What's Wrong with the Implementation?
Okay, so the overall premise of biometric locks is that, in place of the pin-tumbler lock verifying the user is authorized by checking the shape of the key they posess, the biometric lock does a similar comparison using some measurement of the user. The common application has been fingerprint locks, to date, but Facial Recognition is becoming popular as high-quality cameras in embedded devices become ubiquitous, and other schemes have been proposed as well, including iris and retinal scanning, full-palm-print, and more exotic technologies.

If a biometric lock is used only as designed, it should be sufficiently precise, in this day and age, to suit its purpose. Of course, in the case of those seeking to exploit the lock, the use profile is almost never the same as what the manufacturer intended. If biometric devices aren't innacurate because they're worked down to cost of manufacture, they could be worked down to a minimum cost of computing power or storage. Even then, a necessary component of attack prevention - so called "life sensing", is often either easily spoofed or just entirely absent, which is how attacks like the 3D mask attack against FaceID or the "wet gummybear" method of fingerprint spoofing succeed.

Until the underlying problem of making sure the input provided at the sensor is coming from a legitimate source is solved, all biometric security is essentially no better than "voiceprint" locks. If you're wondering why you haven't seen those in a while, it's because it turns out that human voices just aren't sufficiently constant and unique to use for auth tokens.

# What's wrong with the Concept?
## Authentication Tokens Need To Be Secrets
People are going to disagree with me on this, but I'm not sure I can be turned around on it. Modern models of user authentication rely on the multiplicity of factors, but if there's one thing we've learned, its that, by and large, at least one of those components has to be secret.

Take the example of a username/password pair - for better or worse the most common model of user authentication. Almost always, usernames are public in one way or another. That's why we pair them up with a password, which should be used for no other purpose. In this case, think of the login page as less of a door than a corridor of doors. You name the username to select the door you want to open, and the password to open it.

**Biometric Information Isn't Secret!** Everywhere we go, we leave small trails of evidence of our passage. Everyone we meet gets a good look at our face. Everything we touch has latent prints. We lock our phones with our fingerprints and then proceed to tap our oily fingers all over the nice glass surface of their displays.

Attacks against fingerprint locks aren't new. Neither are attacks against facial recogniton. Both technologies are still in their infancy, but time has shown they are slow to mature. For that reason alone, the common avenues of Biometric identification fail the sniff test. Even if someone came up with the holy grail, a rapid-comparison DNA lock, we are constantly leaving hair and skin and spit and allsorts everywhere. Your unique biological identifiers are neither unique nor secret!

## Authentication Tokens Need To Be Mutable
If you've ever reset your password, revoked a public key, or swithced from Authy to Google Authenticator, you've experienced the mutability of authentication tokens. Sometimes things which should be secret nevertheless wind up not being secret. You need to give someone momentary access to an account, or you straight up forget, or you find out you've been compromised by re-using an old password from college, usually used for throwaway systems, that nevertheless turned up in a password dump and now some Russian is using your Ebay account to sell stimulants.

Okay, so maybe the last one was just me.

Whatever the reason, we occasionally need to change our passwords/RSA Auth Cert/OTP seed. It happens, and we shouldn't be ashamed of that fact. When that happens, you should be able to do just that - reset a password, revoke your old key after publishing a new one, and use whatever auth app you want. Here's the problem - if someone captures and releases a high-quality structural map of my face, or publishes the Points of Distinction on my fingerprints, I can't do a damn thing about that - I certainly can't get a new face or hands on my current budget.

This particular problem isn't limited to biometrics - Equifax did a good job highlighting the problem with using SINs/SS Numbers as passwords instead of the usernames they actually are - but it is inherent to every kind of biometric device by their very nature and thus rules them out as a sole authentication source.

# Is There No Hope?
Of course not. Throughout this whole section I've been talking about two-factor identification schemes. But of course, most in the security community already know that we're better off using multiple factors. As part of a multi-factor environment, biometrics do have a place, but that place is more like the RSA token or Security Key than being the password that unlocks the door.

*I hope you've enjoyed this late-night rant on biometric authentication. Look forward to new updates in the future, as the device onboarding guides I've previously tweeted about are still forthcoming.*
