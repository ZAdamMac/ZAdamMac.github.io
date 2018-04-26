---
layout: post
title: "Why Key Escrow Will NEVER Work"
date: 2018-04-26 13:00 -400
categories: Security
---

# What's This Business?
Key Escrow is the formal name for a design of cryptographic system which includes what some people call a "Golden Key." It refers to any crypto system where a master key could be used to bypass the security provided by individual user's keys.

This idea comes up with surprising frequency - most recently in the proposals by the US Government to introduce such systems as mandatory on both hardware such as smartphones and software such as messenger applications.

This idea comes up all the time - particularly in the US, where there is a proud history of people who don't understand the issue their legislating upon legislating upon it anyway. For an example, consider the old Clipper Chip. This idea is, sadly, never a good one. It's flawed at its basic premise and I'm going to explain why.

# Why is Key Escrow Flawed?

## A True Key Escrow Is Impossible
A frequent objection to the implementation of arms control in the US is that such restrictions only make it harder for non-criminal elements to aquire the thing, and this is true of key escrow as well. There's absolutely nothing stopping someone from using existing, secure crypto implementations in the face of such a key escrow system. If you're criminal enough to be organizing something worth spying upon, you're criminal enough to download a reasonably-recent version of GPG which lacks escrow features.

## You can't implement this securely
So, the usual way people propose implementing key escrow systems works more or less as follows. The private side of an asymmetric key (or just the key itself in symmetric systems), is somehow accessable by an authority who can present an appropriate credential. The fashionable proposal at the time of writing is to have the user's key stored on a TPM or other memory on the user's device, which can be accessed either with some credential of the user (a pin, for example) or some credential of an LEO (a key, for example.)

Any who has ever been a renter knows intuitively that this is the Wrong ThingTM. Suppose for a moment you stored all your financial documents and your ID in a locked box in your office. The box has two locks, either of which will open the lid. One is keyed to a special key only you own. The other is keyed to a special key only given to the Good Guys.

### Lock Picks Exist
The simplest, and thereby most reliable (though admittedly most expensive) attack to direct against a crypto system is what's called a Brute Force Attack - you simply try to guess the correct key. Done via scripting, the capability of this attack is limited only by available computing power and the time you're willing to invest. People - like the US Air Force, have built very capable hash-cracking rigs out of networked-together playstation 3s. I've even demonstrated a simple version of password cracking [here](https://www.twitch.tv/videos/253738190) where the only hardware involved is my reasonably-old Lenovo Ideapad 310. 

Granted, this attack works on non-golden-keyed systems too. But the introduction of a second working key increases the likelihood of any one attempt at opening the lock. Worse, let's say I made an exact duplicate of your box, impressioning both locks so that I had a copy of each working key. Now suppose I took my working keys and attempted to open any other box. The key that worked on both is the Golden Key. Now I have the ability to open ANYONE's magic box.

### The Second Lock Needs to Accept MANY Keys
I've been slightly dishonest here. The second lock - the one that takes the Golden Key - needs to accept many keys, not merely one. For understandable reasons, the designer of the box wanted anyone examining it to be able to tell who opened it. Police? FBI? FSB? The only way to have this functionality would be to have it keep a record of *which* key was used, and the keys would therefore have to be unique, at least from organization to organization. Therefore, not only does the brute force attack keep getting cheaper the more organizations are given Golden Keys, but the probability of the first key the attacker finds being a golden key also goes up.

Remember, J. Random Hacker doesn't care if his stolen golden key is for the FSB, FBI, NSA, or NYPD. He just wants a golden key. But wait, I hear you say, each device could have its own unique key. If that were true, there would either need to be a massive central repo of such keys (itself subject to attack) or the key would have to somehow be derivable from some accessable information of the target device. Derivation-based attacks are much simpler and much faster than brute force attacks.

### The Box is a Piece of Junk
Relying on a brute force attack assumes there isn't an even better exploit to open the box of keys without using either the owner's key or a Golden Key. Such attacks, though highly sophisticated, are known to exist already even without a golden key. What's more, the existance of such attacks means it may not be necessary to brute force a golden key - you could steal it from the lock itself.

## The Issues Aren't All Technical

### Jurisdiction is a Son of A Bitch
So far, the loudest voice calling for this sort of governmental back door is the US Government. I take exception to that. I'm not american. Now, you could argue that since US law only applies in the US the manufacturers are likely to only include the backdooring designs in US-sold devices. I don't think this is very likely for two major reasons: firstly, at least where I live, in Canada, we tend to wind up with the US versions of hardware all the damn time, and secondly, if only US devices are so backdoored, then the law is especially toothless - it offers no help to the US intelligence services, and criminals and regular persons both need only order from a foriegn supplier to have unbypassed hardware.

This problem gets even more complicated for software. Shall software companies be bound to only allow download of backdoored versions of their code by clients from inside the US? Who's liable if a US-based crypto enthusiast downloads proper crypto from the UK while hiding behind a proxy?

The obvious solution would be to globalize the push, but here's the thing - if you want all the devices to be so backdoored, you're going to have to share. CSIS will want a golden key. So will the Mossad. Should US lawmakers push for a law that will make every single American citizen more vulnerable to surveillance by the FSB or Beijing? Should US citizens tolerate that sort of a push?

### My Phone is Three Years Old
... And the phone before that I used for nearly five. Is that to be illegal? It would have to be, otherwise those concerned about their privacy need only use pre-escrow hardware and software. Are you seriously going to try and ban people from using the materiel they already own?

### Secure Cryptographic Protocols Fit on T-Shirts
The US (and several other countries) already tried to limit the civilian use of secure crypto by banning its export. This proved to be a horrifically impossible task to actually do. Famously, one young man printed the algorithm for a popular cryptosystem at the time on a t-shirt. At its core cryptography is simple.

Even if no commercial supplier produced proper cryptographic tools, individual developers, criminal or otherwise, will continue to do so. Again, if someone was planning on being the next Tim McVeigh, it would be relatively simple to have a "secure" device and "secure" messaging. Someone planning on committing an act of terror or organizing a criminal enterprise is not going to be exposed because the NSA could read their text messages, and if we can be honest here, no amount of cryptographic hardware or software is going to stop the NSA from doing so anyway.