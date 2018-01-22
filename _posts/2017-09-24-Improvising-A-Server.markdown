---
layout: post
title:  "Improvising A Server"
date:   2017-09-24 08:30:00 -0300
categories: Projects
---

Hi there! I am Patches, the host of the show Hacking With Dog!

... Sorry, I've been on a bit of a Cooking with Dog kick lately. Moving swiftly along, for those who didn't notice, I've set up a multipurpose server in my livingroom that hosts a variety of things for me, which I'm calculating is saving me about $200 CAD annually over my previous hosting arrangements.

Of course, a big part of the motive for the savings was just to avoid trashing my old laptop, the affectionately-named "Slaptop", a mildly modified and badly damaged Acer Aspire something-or-other, c. 2011 or so. Let's get down to how to do this.

# Zeroth Step: Test Aggressively
Alright, to be clear, I am a security enthusiast, not an expert. I'm advising every caution I can see but there's still stuff I could have missed, and moreover, there's going to be some issues with software rot that my have cropped up since I wrote this.

Test any new configuration before going to production - ie, exposing a system to the web. Whether you do this by bashing on the server from behind your NAT and Gateway using another machine or you do what I did and set up a VM network to do the testing on, it's important you do that step first.

# Select and Install a Server OS on the hardware
No matter how elegant your old OS was, the overwhelming chance is that a server OS exists which is lighter weight and cleaner. In addition, a server OS generally is designed to do just that - act as a server on a network or the Internet.

I used the Ubuntu Server 16.04 Long Term Support package released by Canonical. It can be installed from a thumb drive, which is what I did (using Ubuntu Desktop's Startup Disk Creator tool). However, if your old hardware was configured securely, you may not simply be able to boot off of the USB drive. If that happens, go into BIOS/UEFI, and move the USB HDD device to the top of the boot order. 

## Installing Ubuntu Server, and considerations therein.
To be fair, this would have been the fifth or sixth time I installed US on a computer, having used it heavily in my VM lab. As a result I sort of breezed through the settings. There are, however, a couple of things you're going to want to pay attention for:
- When Server asks you for your proxy settings, enter them then and there. It is far simpler to do this in advance rather than to fully install the OS and have to change it later. It's also easier for convenience, as having the network configured correctly lets the installer download and install any updates. Since LTS is getting up there in years, they'll have a few.
- When prompted to install extra packages, make sure to install, at bare minimum, OpenSSL. We'll be using that later in a couple of different ways.
- Do yourself a favour. If there is any data that will be stored on this server besides that which would be publically available (any PII, emails, pr0n, whatever), when prompted enable LUKS. LUKS will require you to present a private secret (usually in the form of a Passphrase) on boot in order to decrypt the entire drive. Do this for every volume on the system (in my case, I only had one). We'll make some configuration changes later to enable you to provide this LUKS secret via SSH.
- Password Protect the root user. The root user's name should be something memorable but unobvious - don't use your name or a common screen handle. When prompted, **do not** enable the encryption of user home directories to provide some isolation between users, unless this is considered necessary. If you do you will need to make special reconfigurations of the ssh daemon later.

## Configuring SSH Access
We're going to use SSH to control this server for the most part. This step is sort of unnecessary, but a great increase in practicaility (via SSH, I can make changes and issue commands to my server remotely, rather than having to dig the old Slaptop out and modify it that way). However, this is not as straightfoward as the usual method because we also have to add an SSH listener to `initramfs`, the micro-OS that, among other things, will prompt us for our LUKS password on a reboot.

### Generating SSH credentials
If you already have your own PGP key you can add an SSH certificate to it in GPG using `--edit-key` as you might expect. I would do this before continuing.

If you're using an OpenPGP Smartcard-compatibile device like a Yubikey NEO it is possible to place the private half of the certificate on the token for added security, which is how I use it.

Either way, if you haven't in advance, generate an SSH certificate and export the public half using `--export-ssh-cert`, and drop it into a file: 

### Assigning the SSH key to Users
Now you need to create, if it's not already present, a directory `.ssh` in the home directory of the administrative user - the one who can use the sudo su command. Once there, create an empty file called `authorized_keys` and cat your key to it using a command like `cat id_rsa.pub > authorized_keys`. In order to do that, obviously, you'll need the file. There's a few different options:
- use SCP or something like it to copy the file to the server
- SSH to the server as the user in question from the computer that has the file and use a standard copy-paste while editing the `authorized_keys` file with something like vi.
- **If you enabled home directory encryption** you need to remap the expected location of each user's `authorized_keys` file to some location outside of the home directory, or else the next time a user is fully logged out and their home directory re-encrypted you will break the ability to log in using a key.

Either way, before proceeding, make sure to SSH to the server using your new key to make sure it's accepted. The next step will force the use of the key, meaning if you lose your key, you'll need to physically access the machine to fix it.

Now we want to force the use of the key - more secure than a simple password. From the server (or an ssh session on the server), become root with the command `sudo su`. From here on proceed with caution.

`vi /etc/ssh/sshd_config` will open the appropriate config file in vi, which requires you to be the superuser. Look for a line that says `PasswordAuthentication`. If it's commented out, uncomment it. Change its value to no. You should also find the lines `PubkeyAuthentication` and `RSAAuthentication` and set their values to yes. Save these changes (esc for command mode, then ZZ to save and quit).

Now restart the SSH service with the command `/etc/init.d/ssh reload`.

### Enabling SSH for LUKS.
If you chose to use LUKS, we now must undergo an additional step. Normally, ubuntu loads into a microOS called `initramfs` to perform the earliest bits of the bootloading process, and this includes prompting for the LUKS passphrase. Normally, the system wouldn't be listening for SSH connections at this time, meaning if your server went hard down, you'd need to physically access it to bring it back online.

This isn't how the pros do it and this isn't how us scrubs are going to do it either.

To do this, first use `cat` to once again add your `id-rsa.pub` to the file `/etc/initramfs-tools/root/.ssh/authorized_keys`, for which you need root. Again as root, reassemble initramfs with the command `update-initramfs -u`.

Reboot now and test that you can supply your passphrase when prompted.

# Physically Placing the Server
So far, I've been working under the assumption that you're working with your improvised server - however you've constructed it - by physically sitting at its keyboard. Now we're free to stop doing that, because SSH is configured correctly. This means we can put the server in its final resting place.

There's a few considerations here, but in essence my focus was to place the laptop-server in such a way that it was visually unobstrusive (my network hub is in my livingroom), and could have a hard line connection directly to my little SOHO router, which provides DHCP and NAT for the network. Once it was placed I also had to change some settings around to make sure it was using the hard line connection - so if practical you may want to have used this hard line connection from the beginning.

Nothing about the placement of your server provides it with any security whatsoever so that decision is left as an exercise to the reader.

# Selecting Services
I don't know what you want your server for, so you're going to need to select, install, and configure your own services (Apache, NAS, Plex, whatever). This is mostly left as an exercise for the reader, but remember to test these out on your emulated set first.

Also it's important in this process to record which ports your various services use as we will be dealing with those in the next step.

## A note on email:
Most ISPs providing residential servers block port 25, at the very least outbound. This means your improvised server is not entirely appropriate for hosting email services.

# Network Provisioning and DNS
So, we have a server on our network, but anyone who's ever done much playing with network processes knows that both externally (relative to the rest of the internet) and internally (your LAN), your computer's IP is far from being static by default.

This is undesirable. We're going to be attempting to connect to our machines via SSH, we presumably have services on the server that other machines will need to talk to, and if you're using your server in a web-facing way, you'll need to be able to tell the router what the hell this machine actually is.

## Static DHCP on the LAN
I am a huge believer in making your DHCP leases for your LAN as static as possible. Not only does this make it easy to tell at a glance which hosts on your network are guests or impermanent, it also helps in terms of addressing.

Even if you don't go that far, it's helpful to know how to do a static mapping on your network, and frankly, it's entirely necessary for two classes of devices - printers and servers! Since your server is one of those, static map it.

Point your browser at your AP's network interface (usually, in my experieince, `192.168.2.1`, login and find the relevant section. Since I don't know what you have for your router it's RTFM time. You're going to need the server's MAC address. If you went like I did and used Ubuntu, you're going to want to use the command `ifconfig | grep "HWaddr"`. This will show a list of MAC addresses for every interface currently attached. You'll be looking for a device along the lines of `enp1s0`, but thanks to SystemD fuckery, it's impossible to tell you exactly what device you're looking for.

## Extra Considerations if you're using the internet
Right now, internally of your network, you don't have to bother with port forwarding. This could change, however, if you're doing anything web-facing. If you want the private dropbox implementation or web page you're using to work for users outside your network (including you if you're not at home), you're going to need some level of DNS.

There's a bunch of ways to go about doing this but as a residential users you have two conditions - you need a registrar you trust, and one that supports Dynamic DNS (or will let you set your DNS records to point, directly or indirectly, at a dynamic DNS providor). If you aren't hosting a web page you can even settle just for dynamic dns through something like no-ip. This approach is really popular for people hosting game servers and the like.

Either way, you need to do some DDNS setup now. Your DDNS providor probably has a client they use to keep their record up to date. This should be installed on your server and is often available over `apt-get`. Your router may even allow you to set up DDNS credentials directly for the most popular providers.

Once you have DDNS you'll have to go into your router and work with port forwarding. TCP/UDP ports are how computers communicate with one another in this day and age, and they're why, as discussed above, you probably can't host your own email service. Shame.

Remember how I told you to make sure to keep a list of the ports your services use? Now we put them into use. Log back into your router and forward each of those ports - and only those ports which you need - making sure to point them at your server's shiny static IP.

## I've done the port forwarding, but when I point my browser at my shiny new domain name, I get redirected to the login page for my router.

This is an issue with some routers I've seen - including mine - that prevents a special kind of loopback, and is the source of your problem. Essentially, what is happening is the request you're making is being stripped of its port number and, on coming back, is not being forwarded effectively.

Use a mobile device or have a friend try your domain without using your network. If they get where they're supposed to go, rather than your router, this is the issue. Investigate your model of router to see if you can enable the function we're missing.

If not, you'll have to add an entry to each machine's HOSTS file (ubuntu: `etc/hosts`) to tell them to point requests for your URL to the server's static IP. If you are doing this on a static system like a desktop, all done.

If, like me, your daily box is a laptop, you'll probably find you aren't always on your own network, which was likely the point of exposing all this to the internet in the first place. You CAN re-solve this problem when out by editing HOSTS again to comment out the redirect.

Alternatively, you could write a script that could either on-command or automatically add or remove this entry, possibly based on your current network SSID. There's a variety of ways to achieve that end, and owing to that, I think it would be most fun to leave that as an exercise to the reader.
