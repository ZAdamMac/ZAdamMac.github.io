
----
layout: post
title: "HTB Post-Root Writeup: Frolic"
date: 2019-03-23 08:00 -300
categories: HTB
----

*I originally attacked Frolic (and wrote this article) in October 2018. It was the third box I'd ever claimed root on at HTB*

Frolic was a box with some mixed reviews - and I have to be honest, when I started to tackle it, I was in the naysayer camp. That being said, while it wasn't the most realistic challenge ever, it was the most interesting machine I've tackled on [Hack The Box](https://hackthebox.eu) to date. The complications come from elements of the early attack on the box which are more in line with a CTF than a "realistic" penetration test scenario.

That being said, my favourite games lately have been Fireproof Game's The Room franchise, and before that I had played and mostly-beaten Myst, so puzzles are fun from where I sit.

# Phase 0: Initial Numeration (Heh)

We learned lessons from Irked, and among those were to scan for every damn port. For that, I have my convenience call:

```commandline
nmap -sV -v -p - 10.10.10.111
```

Which returns the usual samba and ssh ports, as well as a 1880/tcp service for something called "Node Red" and an Nginx instance listening on 9999. I'm pretty sure the samba ports are there for HTB themselves to manage their systems; ssh is a little more useful but this returns a recent enough version number that I doubt we can just jump in from there.

A quick point of the browser at each of the other open ports reveals a simple nginx status page (with a typo) on 9999, and a login page for Node Red at 1880.

I'll be straightforward with you - I spent two evenings throwing every trick I could think of at 1880 to try and login. The login page seemed much more functional than the default-looking status page on 9999 (and besides, part of that page pointed you back to 1880!). I never managed to log into the Node Red service, and as near as I can tell, nobody else did either.

By this point I wonder if there's actually anything at 9999. I have my own program for this, bruteRoots, but it's not very stable. This time, I break out dirbuster. Using one of its default wordlists I pretty quickly get a few results back:

- a /test/index.php document which spits out a bunch of debugging information about the server's current version of PHP.
- a /backup/index.php document which spits out the message `password.txt user.txt loop/`, but otherwise does nothing else that I can see.
- an /admin/ directory with a hand-coded login prompt.

# Phase I: Don't Roll Your Own Crypto, Kids.
The developer tools of most web-browsers are underrated apperatus as far as I'm concerned. Most hackers seem to discard them as just a way to get a quick defacing, but I've found interesting stuff in the past using these tools, and I have to be honest, they came in handy for Frolic.

This admin page has a hardcoded password in its own javascript. I felt pretty clever for finding that, so I went ahead and punched it in directly.

When the resulting string of hot garbage spat out, I wasn't so sure I was still clever. The resulting success.html page's only meaningful content was a "paragraph" of five-character words, composed entirely of ".", "!", and "?". What the actual hell?

I like puzzles. My wife really likes puzzles - she played all sorts of ARGs through college and got pretty good at it. We were both stumped, though.

A friend tipped me off to the Esolangs wiki, where I found [this page](https://esolangs.org/wiki/Hello_world_program_in_esoteric_languages) listing Hello World in a number of peculiar coding languages. Extra irritatingly, none of them caught my eye particularly. I did, however, find a translation/execution environment for a language called Ook, which included an option to parse the language without the requisite "ook" strings.

This seemed to do the business! What I got back was also nonsense, but it was nonsense immediately recognizable as base64. Decoded, this turned out to be a serialized zip file.

One problem, though: That's locked too.

# Phase II: Cracking the Zip File
I spent about half an hour learning how fcrackzip actually works and getting a good wordlist to use with it. At runtime once all the pieces were in place, the correct password was returned to me within seconds. It was "password", and I feel appropriately stupid for not just guessing that.

The zip file yields an internal file called "index.php.", but in reality the file itself is just one long hexadecimal string. Fortunately, php has a function called hex2bin. Execute that, and this string returns our first real credential - I hope. It could be a troll.

```commandline
idkwhatpass
```

# Phase III: Initial Enumeration
Earlier, when I started finding directories with dirbuster, I stopped. When I found this bizarre password, I imediately took it back to Node Red and tried that, but no luck. For a while, I was stuck again.

Zen Mind, Beginner's Mind, though. I must have missed something during enumeration, so I spin the dirbuster back up and go looking for other directories I may have missed. In the end, I'm glad I did. I eventually find a few sub-directores of /dev/ which I missed, and those, eventually, lead me to a login page for a service called PaySMS, where I can login as admin with the password we tried so hard to get.

# Phase IV: Exploiting PaySMS
It's important to note now that it is late, late indeed on the third night of attempts of this machine. After some time to sleep on things, I return to the problem.

Manually, I check out each and every page the UI will allow me to interact with here. Eventually, I do find one - the send from file dialogue - which allows the upload of an arbitrary file. There's relatively little sanity checking here, but at the same time I can't just throw up my usual php reverse shell and expect a strong result.

In fact, I spend the entire fourth evening and a good hour of the fifth day trying to come up with a CSV Injection Attack to get me a shell - any shell will do. Frustratingly, no variation I try works. Even with access to [the relevant source code](https://github.com/antonraharja/playSMS/tree/master/web/plugin/feature/sendfromfile/docs), I'm just not seeing it. It just won't happen for me.

In frustration, I fire up armitage, which I still find to be the most convenient way to manually search the exploits I have installed in my current MSF setup. Sure enough, there's two for playsms and one specifically named for the CSV Upload vulnerability I suspected existed. (I would later pull the exploit code about to try and see what I was doing wrong and fail because I don't currently parse ruby well.)

Sure enough, actually deploying that exploit worked just fine.

# Phase IV: Shell-to-User
Fortunately, after all that nonsense just to get a shell, user was free, even with MSF/Armitage's very limited default shell. You can `cat` user.txt right out of one of the users' home directories.

# Phase VI: Limited Enumeration
I still have trouble handing off shells. I couldn't find a way to get a netcat shell thrown back to Kali properly, and without that there's no real sense in leveraging python's pty;pty.spawn trick, because the armitage terminal does such a god-awful job of rendering it.

Fortunately, I don't need a good terminal. A simple `ls -ahlR` from the same directory where I found users.txt reveals a hidden `.binary` directory with a single file inside called rop. This has sticky permissions, is owned by root, and is executable to my shell's otherwise unhelpful `www-data` user.

Rop is, of course, binary. Cleverly or not, I throw this file back to my kali box to have a look at. I figure, worst comes to worst, I don't store anything on this machine anyway. If something blows up, I can just restore a snapshot and move on.

# Phase V: Assembly? Never Heard of Her
It's not unduly modest to say that I am not an expert in assembly, even assembly of the x86 variety. I know that there are different flavours of assembly, and I have a rough idea that assembly instructions are basically operations against specific addresses in memory. But that's about it.

In goofing off with the binary, I can see that it claims to send whatever argument follows as a message to somewhere. In an effort to find out where (thinking this was important), I need to learn about some tools quickly - `gdb` and `strings` and a whole host of other, smaller tools.

Eventually I realize that all this does is cat my argument to the end of a string saying a message was sent, and print that. Which is fine. I don't need fancy.

I spent a lazy evening watching videos on exploiting printf functions and I eventually come up with a plan of attack - my brazen, not-knowing-assembly-besides-hello-ass is going to write me a buffer overflow.

Some googling eventually reveals the String Format attack, which isn't quite what I want, but this leads me on a tangent pointing me back to buffer overflows. Basically, what happens is this:
- The program expects this string to be of a certain length, or less.
- The program finishes reading the buffer storing the string and then moves on to the next instruction, and;
- You can crash the program on purpose by exceeding the minimum message length, which makes the next instruction a "wrong" value depending on the string provided, causing a segmentation fault.

It's not a stretch to think from there that if you'd used the RIGHT too-long message, you could put an arbitrary memory address at the end of the buffer and get the program to jump into some other task.

### How Long is Too Long?
In this case? 52 characters.

There are a bunch of ways that could work that out. I could have checked to see if there was a difference between the exit codes reported by crashed and normal exits and written some python to just add a character to the message until it crashed. That seemed unweildy though.

Turns out metasploit has a tool for this: pattern_create.rb. You can spit out a pattern of arbitrary length where the pattern does not repeat. A companion tool can reverse that process later. Grab a string of length ~200 or so, throw it at the program, and it'll crash.

How do we know where, though?

It turns out, command line debuggers have some use. `gdb` proves quite helpful here. If you start the program with `gdb targetProgram`, then run it with the interactive command `r` followed by your pattern_create output string, the program will still crash. The difference is, this time gdb is there to catch where it crashes.

Take that memory address and feed it to pattern_query.rb and it will (with some thought, potentially), spit out an answer. It choked on 52. 

### Back to the victim machine
We have to return to the victim machine at this point. In my reading I learned that there's a pretty simple way to go about this, if all you want is a shell. Initiate the overflow, point the next instruction at your language library's (in this case, libc) equivalent to an os.system() command, have the next instruction be the exit function, and the final instruction be an address pointing at a string that can do a shell, ideally `/bin/sh`. Sweet. No problem.

Unfortunately, there's a lot of moving parts here. Different installs - hell, different *boots*, are going to have different libraries living in different locations in memory. Worse, if ASLR is enabled, that memory is constantly moving around to increase the difficulty of an attack.

We can find the relevant library on a given machine, from that machine, with the following command:
```commandline
ldd /home/ayush/.binary/rop | grep libc
```

This will spit out the libc library in use, the absolute path of the library, and it's current address in memory. If you're on a machine even a little bit recent, run this command twice. If you see the memory address changing, that's an indication ASLR is active. Fortunately, if it's not changing *much*, we can just brute the position.

While it's possible to determine the right string to use by hand, it's far easier just to find the relevant values and punch them into a script, which I've done. Where we already have the `overflow_limit` and `libc_address`, we really just need to find the memory offsets for the various points inside libc that we need to know.

You can get the offset for system and exit by using `readelf` on the library binary and grepping for, well, system and exit. The location of the "/bin/sh" string is a little harder though. Doing that involved the use of another program called `strings`, and to invoke it thus:
```commandline
strings -a -t x path/to/library | grep "/bin/sh"
```

Fortunately that does indeed yield an offset.

### This Plan Needs Snakes

As I said, it's totally possible, given all this information, to construct the relevant string by hand. This would even have worked on Frolic, since Frolic wasn't even using ASLR. However, for the sake of completeness, I wrote a quick python script to exploit this binary for me, and tried to make it general enough to be useful again any time I think I need a printf exploit - I should just have to update the path to the target binary and the relevant addresses/offsets.

Anyway, I got the addresses from Frolic, plugged them into my script, threw the script over to my Frolic shell using netcat, and...

Nothing appeared to happen. I'm nervous. It's been probably 20 hours now I've worked on this box. Slowly, I enter my next command.
```commandline
$ whoami
root
```

Perfect. We can `cat /root/root.txt`, clean up our mess, and go the hell home.

## Securing Frolic

With the massive caveat that there's absolutely no good reason to discuss security practices and deliberate CTF puzzle-boxes in the same breath, there are some things that would have helped.

As mentioned, I did not gain access to the Node Red service on this box. There's no reason that the PlaySMS service couldn't also have been that hard to log in as. If it was, my quite little CSV shell wouldn't have been possible, and none of the rest of the exploit chain would either.

Then there's the matter of the arbitray CSV upload. It's my understanding that this is patched in more current releases of the PlaySMS utility. Of course, if I'm wrong about that, there's still a few ways to sanitize inputs. When accepting user input, you should always take this method.

As for the rop binary, the same goes. If this printf() functions were properly sanitizing user input, particularly if the input was clipped to the length of the buffer, I wouldn't have been able to overflow that buffer. I may still have been able to find another path to root, but it wouldn't have come from that direction.