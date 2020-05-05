---
layout: post
title: "HTB Post-Root Writeup: OpenAdmin"
date: 2020-05-05 13:00 -000
categories: HTB
---

OpenAdmin is a  [HackTheBox](https://hackthebox.eu) machine that was recently retired from their active selection, but is still playable on the VIP networks. I decided to make an attack on Obscurity back in April 2020, as part of my general effort to get better at this sort of thing.

On the whole, this was a nice fun box that leveraged yet another tech stack I happened to be familiar with. While initial foothold was (and always is) tedious, this whole box is actually pretty straight-forward. I burned a lot of half-known things to ROM working on this box, and it was a good excuse to dust off and start using a tool I rarely bother with: `msfconsole`.

Grab your fluids of choice and let's go through how I attacked the box, and then at the end talk a little about how to keep people like me from running (as) roughshot across it.

## Step I: Initial Enumeration and Foothold
As with every HTB machine, you actually *start* your attack on OpenAdmin knowing it's exact address on the LAN. This meant that while I was running my usual `nmap -A` command to see what was up on the machine, my usual habit of punching the IP into a browser actually returned a result - the Apache splash page. This wasn't much use besides providing an Apache version, but none of the MSF modules that were for that specific Apache version worked well for me. That said, YMMV.

I also wound up running `ffuf`, a golang-based URL fuzzing tool, against the site. This turned up three "subdomains", manifesting as subdirectories of the server root, which provided hours of frustration as I browsed around them, trying additional runs of the fuzzer and looking for some of the more exotic easter eggs of the past.

My error here was in the ffuf command I built, which used the american english wordlist built into the OS as my wordlist for fuzzing. I tried enumerating again later using `dirbuster` and its suite of wordlists and almost immediately found `10.10.10.171:80/ona/`.

This exposes a portal for Open Network Admin, which helpfully tells us that is an instance of an outdated version! If we  search exploitdb for this application and that version, we find two exploits:
- a shell script that exploits it, apparently written by the developer of the program, and;
- an MSF module that exploits it, deploying *an arbitrary payload*.

I decided that I wanted to use the Metasploit Module, mostly because I know that Metasploit is considered a professional tool for this sort of thing, and I have been pointedly avoiding the use of MSF in preference of "do it myself" exploitation, which is slow, clumsy, and starting to reach the point of being redundant.

Selecting the exploit and the x64 version of the meterpreter shell payload, it was pretty quick to get a session. From there, drop into a shell, and:

```
whoami
www-data
```

## Step II: Give me that Sweet User Flag
### This is Jimmy

As anyone who's done a half-dozen or so of these knows, being www-data sucks for a few reasons:
- www-data can't usually log in, so if you disconnect for some reason, you need to repeat your exploit
- exploit shells like one-liner reverse shells are clumsy, and meterpreter itself is only a little better.
- www-data *usually* isn't worth a flag.

With that in mind, there's still some more enumeration to do. I ran a copy of [this script](https://highon.coffee/blog/linux-local-enumeration-script/) which I netcatted into bash, sending the output back to me in the same way (to try and minimize my disk usage). I didn't find much immediately obvious or helpful in that script, but I would keep coming back to run particular parts of it later. What was interesting (in terms of this being an HTB box), was that there are actually two users in the home directory: jimmy and joanna.

I did notice that there are some config files in the /var/www/ona directory. One, `database-settings.inc.config`, was hilarious:

```php
<?php

$ona_contexts=array (
  'DEFAULT' => 
  array (
    'databases' => 
    array (
      0 =>wor
      array (
        'db_type' => 'mysqli',
        'db_host' => 'localhost',
        'db_login' => 'ona_sys',
        'db_passwd' => 'n1nj4W4rri0R!',
        'db_database' => 'ona_default',
        'db_debug' => false,
      ),
    ),
    'description' => 'Default data context',
    'context_color' => '#D3DBFF',
  ),
);
```

I did monkey around with dropping the meterpreter shell and elevating it (via pty) to get access to the DB, which did have some lovely passwords in it that weren't actually useful for anything as they were system defaults. However, upon reflection, `n1nj4W4rri0R!` is the sort of password a user would use!

At a blind guess, I decided jimmy was the most likely user to use it. Sure enough, you can ssh into the box as jimmy using that password, so it's really dual purpose.

```
cat user.txt
```

Sadly, Jimmy doesn't have access to the flag! As well he shouldn't, with habits like reusing passwords!

### On Second Thought, I'd Rather Be Joanna
From a puzzle/game design perspective and in retrospect, I found the way you get from being Jimmy to being Joanna rather clever. That said, I hope the scenario itself is relatively unrealistic.

We know Jimmy doesn't have the control of the flag. But we also have control of Jimmy, which means we can get a list of what he has control of using:

```bash
/usr/bin/find / -user $(whoami) 2>/dev/null
```

What this does, is invoke the `find` binary, starting at the filesystem root `/`, for the user we're currently logged in as, and dump all errors to /dev/null, because we don't want to see them.

In doing this, we learn that Jimmy has access to a whole subdirectory in apache's files, `/var/www/internal`. There isn't much here, but reading the few files that make up this small site is instructive:
- A main.php, that doesn't do much anything if you arrive there improperly without logging in; if you are logged in, it will echo the output of `cat /home/joanna/.ssh/ida_key`
- A login.php, that validates a the post data from the form against a hardcoded then redirects you with a valid session to main.

`Reversing` this intimidating SHA512 hash is easy, not because SHA512 is flawed, but because it's insufficiently salty. We can just look up the value in a table, yeilding the password `Revealed`.

However, this site isn't actually accessible via the browser from the attacker's machine. If you know apache2, though, you know that you can get a list of the sites available by pulling:

```
ls /etc/apache2/sites-available
```

Sure enough, there's a file for an internal site, `internal.conf`, and when you cat that out, you see that apache, by default, wants to present that site on a high-order port listening at localhost. We can also see some cleverness that would have kept you from just catting out the key as www-data. There are a few different ways to connect to that site. For me, the easiest was a quick curl command:

```
curl -XPOST -d 'username=jimmy&password=Revealed' 'http://127.0.0.1:52846/main.php'
```

What this does is send an HTTP post request to the right port on localhost, specifically posting to main.php, with the HTML form data for the username and password. The response that comes back includes what is likely to be Joanna's private key!

### My John is Too Skinny!
Some people might have found it obvious that you can't just import a key like this, but I freely admit to struggling for a few minutes before realizing that the encrypted key would need to be decrypted. Despite the reminder to keep my ninja password in mind, I just couldn't guess the password for it.

Thankfully, I have a friend who can make password guesses much much faster: `john`, better known by most as John the Ripper. John is a security audit tool for making very fast guesses of passwords in situations where the target isn't rate-limited, like when you have a copy of `/etc/shadow`.

Unfortunately, even after parsing this key with the invaluable `ssh2john.py`, I couldn't get john to recognize the hash type being used, so I couldn't get my password. After some frustrated googling, I discovered that the version of john in ubuntu's `apt` repo is 1.8.0, making it a little outdated. There is a more modern version, called "Jumbo" by the development team, that can be run against ssh hashes like the ones produced by ssh2john.py. Fortunately, that's available as a snap, and invokable as `john-the-ripper` in a bash shell.

It ran quickly enough against the rockyou.txt password list, and in spite of  warning that this password type could yeild false positives, it returned only one password, `bloodninja`.

With that password, you can quickly decrypt joanna's key using OpenSSL's `rsa` operation, and then you can `ssh -i` into the box from your local machine as Joanna. Even better, Joanna has access to the user flag!

### This was fun, but was it realistic?
It's a little hard to say. I can't imagine anyone who would be in a position like Jimmy's - how is it possible that he would need to retrieve Joanna's private key, but not be a sufficiently privledged admin to just replace it on her? And if it's not for Jimmy, why does Jimmy have control over that application?

Either way, a very fun little puzzle.

## Step III: Easiest Root Ever
I actually went to bed at this point, knowing that I had a reproducable shell as a user. I almost wish I hadn't; when I sat down the next day, root came very quickly. There was nothing of interest in Joanna's home directory or the list of files she owned, so I ran another favourite, `sudo -l`, to drop out all the things she was allowed to use as a super-user, hoping to find something exploitable or weird.

`nano /opt/priv`, without the use of a password, wasn't exactly what I had in mind. I couldn't quite figure out what `/opt/priv` does. The file didn't already exist when I nano'd it, and because I didn't end up saving there, it didn't exist after I had.

What this `did` allow though, was for me to use `^R^X` to induce nano to give me something not unlike a shell, which can become an actual shell with the command `reset; sh 1>&0 2>&0`, though this results in something not unlike a reverse shell in terms of its limited utility.

Of course, that limited shell is now running as root. You can `cat /root/root.txt` with it, and it's not hard from there to do something like the pty trick to get a reasonably tty-like interface to use as root.

We own the system at this stage, and can do what we want with it. If you're on a server that other people are using, that should be exactly nothing. Don't make a mess!


## Blue Team: What Went Wrong?
While I would at least hope that the user privesc attack wasn't that realistic, it's still helpful to consider all the stages of the attack-chain, figure out how you'd know they'd happened, and what to do about of them:
- The initial compromise of OpenNetAdmin (www-data)
- Procedural Use of a Password and Reuse of Credentials (Jimmy)
- Credential Leaks (Joanna)
- Unintended Use of SU Privileges (root)

### Compromising OpenNetAdmin
The flaw in this particular version of OpenNetAdmin wasn't just known to the developers, it had [a metasploit module](https://www.exploit-db.com/exploits/47772) that exploited it; one that came preinstalled as part of msfconsole by the time I was using it to do this in April 2020. As a support weenie and sysadmin, I know better than some of my fellow security novices that "just update everything" isn't always a solution.

**Indicator(s) of Compromise**: The exploit itself involves making a POST to a vulnerable endpoint, embedding the actual command payload (Command Injection Attack) as part of a string of ajax arguments in a bit of url-encoded form data. If ONA cannot be updated to a version that has patched the vulnerability, `apache2` should be configured such that POST bodies to the ONA directory and its contents are logged, and that those logs are further parsed to look for the malicious payload. While the payload portion could be obfuscated, much of the 'setup' for the compromising string appears unique.
**Suggested Response to Compromise**: If it's suspected that ONA was compromised, investigation should be undertaken to determine if there was any unusual process generation on the part of www-data. www-data is not normally going to invoke much besides apache in this case. In particular, the appearance of an IOC for this vuln should start a search for the IOC(s) for the next step in the kill chain, as well as any necessary remediation.
**Was this risk acceptable?** Maybe, if there was sufficient monitoring in place to look for this and other compromises of ONA.

### Use of Password-Based Credentials by and Application, and Reuse of Credentials by a User
The path from `www-data` to `jimmy` was paved by a sort of double-failure: the use of password-based authentication for the ONA connection to mysql for its controlling database, and that password being 1:1 identical to jimmy's user credential. It's unclear in ONA's sparse documentation, but some discussion on their issues page seems to suggest that it would have been acceptable to use Unix sockets as the authentication method to the DB instead. This would allow a totally different workflow:
- If Jimmy needs to access the DB, he should have a second administrative (mysql) account for making changes to it.
- Jimmy's password wouldn't have been reused for the ona_sys database password.

Ideally we'd been killing passwords altogether but `baby steps`.

**Indicator(s) of Compromise**: This is a little hard to factor in, at least for me. What I would be looking for, if logging was available, were instances of the `~/local/database_settings.inc.conf` being read shortly after the Command Injection compromise was detected. This file should only be being read from when ONA is starting (to my understanding), so extra reads of this file by `www-data` that aren't explained by the apache2 service restarting are `highly suspect`.
**Suggested Response to Compromise**: The password to the database, if being used, should be changed. However, it would be better to use sockets entirely. This would be a good time to review www-data's mysql history (as well as the history of changes to the ona db in general) for anything that's unusual, which means you would also need a baseline of the normal activity patterns for the DB!

### User Credentials Leaked by Application
Let's assume for a minute that we had an actual mission need for the /internal/ "web" service to exist. Any use of this application, period, is an indication of compromise, because:
- It is tied to a credential known to Jimmy (hardcoded to it, in fact.)
- It returns, by design, a credential known to Joanna, and;
- A compromised private RSA key of any likely keysize, even passphrase-protected as was the case here, can be relatively trivially reversed out.

This application shouldn't exist. If it's triggered, an alert should immediately be sent to the responsible security individual to go scold Jimmy, and Joanna should be told to replace her key immediately.

### Unintended Use of Sudo Privileges
If `NOPASS:/bin/nano /opt/priv` did what the admin who set that up likely thought it did, then the only thing Joanna would be able to do with it is use nano to edit the file /opt/priv. Unfortunately, what it actually does is let you run nano, including its code execution features, as root, without any need for any kind of authentication on the user's part.

**Indicator of Compromise**: `joanna` issues the command `nano /opt/priv`. Seriously, that's it.
**Suggested Response to Compromise**: Confirm whether or not an /opt/priv was created. If Joanna is a sufficiently trusted user, also confirm why the command was issued. If the file is missing or J's answer is not satisfactory, run the response as though you had a root compromise on this machine.

## Final Takeaways
I loved this machine! While the path from Jimmy to Joanna probably wasn't that realistic (I hope), both foothold and final privesc happen all the time. I can't tell you how often, as a sysadmin, I run across scheduled jobs that are running as something like `root`, or `local admin`. Hell, I've seen scheduled jobs on windows platforms running as `SYSTEM`.

This is a good machine to sit down and practice the basic workflow of enumeration, search for exploit, compromise, and escalation, but it has enough of a puzzle/game element to give you that "escape the room" thrill as well. I would even recommend this as a potential *first* box if you're tired of doing challenges on HTB and want to move on to machines.

HackTheBox is a nominally free platform, but I still pay for a VIP license so that I can have access to quieter versions of the network (and work on some older boxen). All that being said, if you wanted to contribute to this project of documenting as many of the boxes as I can financially, your best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).

