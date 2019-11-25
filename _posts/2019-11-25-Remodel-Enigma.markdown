---
layout: post
title: "(Re)Modelling Enigma: Misadventures in Emulation"
date: 2019-11-25 08:00 -400
categories: Projects Toys
---

Around this time last year, as a joke that roughly corresponded with the anniversary of the birth of the man who headed the design of the damn thing, [I implemented](https://pypi.org/project/python-enigma/) a general solution that was meant to account for most models of the Enigma cipher machine. Enigma is a polyalphabetic substitution cipher of some renown for its use by the THird Reich during WWII, and breaking the enigma is considered a seminal point in both the allied odds of victory for that war, and the history of general computing. This is a story told well by better and I strongly encourage you to break into it.

A couple of weeks ago, when I was trying to cram a few last slides into my deck for BSides Fredericton (where I gave a talk, amusingly, on failure), I recieved a message from an erstwhile user  of the package. This individual was using my code (their first mistake) in order to help decrypt the large number of latent engma messages that were never broken, a tassk that is at once both hurculean and critical to the history of the 20th century, and a simple matter of computing power. After all, the first single-purpose computer was arguably Turing's Bombe, made for the very purpose. Modern computers are orders of magnitude faster and capable of processing several dozens of such messages in parallel - the fact that they are Turing Machines being relevant here as that makes them (definitionally) suited to emulating something like the bombe.

It seems this user had obtained a copy of a message - known broadly as the Dönitz Message - and was attempting to run it through python_enigma to try and simulate the action of a naval M4 enigma machine. This was a very good test of the package's capabilities as all the necessary key information for this message was known, along with both the plaintext and ciphertext. Quite naturally, python_enigma wouldn't even come close to decrypting it correctly.

So begins the adventure in rebuilding an Engima Machine. After all, I'm a whole year wiser now. This shouldn't be a hard fix.

## What I Actually Built
The first thing I had to ask myself was why this machine didn't work correctly. After all, I had tested it - the package comes with a module called `helloworld` that goes through the motions of encrypting, and decrypting, the message 'HELLO WORLD' with a known set of key configuration. This should prove that it is working correctly, right?

What it actually proves is that whatever kind of machine my code is emulating posesses a single quality of the enigma - a vague similarity in the manner of operation. Put simply, this means that like the enigma, for a given initial state, my machine has no idea if it is decrypting or encrypting the message.

It should also be important to note that while most programs implementing Enigma do so as either an abstraction or purely mathematically, I was more interested in constructing a simulation of the mechanical state of the machine under test. I acknowledge that this is both harder to read on a code-level perspective and quite likely less performant (given python's Global Interpreter Lock) than perhaps possible. It was also, I thought, easier to understand on an implementation scale. I thought wrong.

## What I Got Right
### Stecker Board
The enigma machine had a system of doubled plugs - not unlike audio jacks - on a board on its front face. This system has been known variously as the stecker board or plug board, and was a method of adding complexity to the machine. The operator could add a cable connecting any two letters - "AB", for example. This performs a sort of doubled substitution in the following way:
1. A signal passing through the steckerboard on its way from the keyboard at position A leaves the steckerboard at position B.
2. A signal returning from the wheelpack by way of the stator passes through the steckerboard on its way to the lamp, and is again converted.

These substitutions are bidirectional - if A "stecks" to B, B likewise "stecks" to A. I implemented the steckerboard as an object containing an initialization vector, a dictionary to contain the substitutions (in both directions), and a convenience method to perform lookups against that dictionary. Thiss worked flawlessly every time it was used.

### Stator
There were two stators, or arrangements of key-to-alphabet pinning, around the circumference of the wheel pack. The most commonly used or "military" stator goes around it's alphabet wheel starting with A and proceeding through the alphabet as normal. There was a civilian model also in use, where instead, the ring ran in keyboard layout order for the enigma's QWERTZ keyboard layout.

I implemented these as, you guessed it, dictionaries. It works fairly well for the military method. I'm not sure if it works for the civilian method but as most interest in Engima surrounds military messaging and the military machine models, I haven't really had cause to test it.

### Overall Order of Operations
In general, to encipher a single character on the enigma, you do this:
0. A key is depressed, indicating the character to be enciphered.
1. This key passes through the steckerboard and is substituted if needed.
2. This key passes through the stator into the wheelpack.
3. This key passes through the wheelpack in right-to-left order, then through the reflector, then back through the wheelpack in reverse order.
4. This key passes through the stator into the steckerboard.
5. The steckerboard makes any necessary substitutions.
6. The appropriate lamp lights to indicate the output character.

And, in general, I did all these steps in the right order.

## What I Did Wrong
### When and How the Wheelpack "Steps"
A key cryptographic feature of the enigma's polyalphabetic substituion cipher was that the substitution changed with each keypress. This was achieved by means of the incremental rotation of rotors in the wheelpack, through a mechanical process best described in a moment. In my original implementation, I had this step take place *after* the key in question had been encoded. This is not in fact the case. For mechanical reasons, the rotor advances *before* the signal passes through.

I'd hoped simply moving my rotation code to the top of the appropriate code block would resolve the issue, but it did not.

#### The Most Common Enigma Step-Case: The Pawl Method
The most common versions of the enigma - M3 and M4 - advanced the rotors by means of a notch-and-paul mechanism. Every rotor had one or more notches around its circumference, which would allow a pawl in the mechanism to engage the rotor to their immediate left and advance it. This has a few really interesting implications:
1. The behaviour of the wheelpack is not like an odometer, though the left wheel always steps.
2. Because of the way the notch is formed, if a wheel is advancing, so must the wheel on its right.
3. No wheel can ever advance more than one position in a single keystroke.

Rule 2 is interesting. It can be counter intuitive. In a three-rotor enigma, we would only observe it if the leftmost rotor stepped, because the rightmost rotor *always* steps. I had correctly  modeled for rules one and three, and made sure the roll-over took place at a notch, but I hadn't implemented this rule. I do so now, and honestly it actually made my rotor advancement code slightly more compact as a result.

The absence of a correct implementation of this "double stepping" mechanism and the late run were both contributing factors to the incompatibility of the system.

#### Modelling the Internal Wiring of the Rotors
The Rotor object itself is probably the most complicated thing in the whole package, at least in terms of the difficulty of holding all the state at once in one's head. This is a because a rotor has the following attributes in particular:
- **position**: a rotor has a position (expressed as an index with A=0) which determines at what point through its full rotation it is currently resting. This is used chiefly to calculate positional offsets, which we'll discuss is another step.
- **wiring**: A dictionary that maps an entry (right-hand) pin to an exit (left-hand) pin. Pins are numbered starting from A (from the rotor's frame of reference) as 1.
- **wiring_back**: A reversed dictionary of the same, used for computing the path of signals returning from the reflector.
- **ringstellung**: The Ring-Setting, which determines the position of the rotor's conception of A relative to its actual internal wiring. This was originally expressed as an index, like position. I've since changed the model to use a number, with A=1

In particular to this problem, the first three points are crucial. I had originally imagined position as an index that one could apply mathematically to both the entry and exit pins and get an appropriate result out. This proved to be slightly incorrect - while it works in principal, there are a few strange combinations of ringstellung and position that were causing unexpected behaviour.

I addressed this issue by re-imagining the space between the rotors as static rotors which never advance, and which simply pass signals through (A maps A, B=B, etc). Using this I could create two ad-hoc dictionaries for each wheel, which translate from the arriving position on the wheel-face to the exit position on the opposite face in neutral terms. This made it possible to calculate positional offsets for each wheel *without* needing to make reference to the wheels, stator, or reflector on either side of it.

#### Modelling the Internal Wiring of the Rotors

Remember the properties of a rotor:
- **position**: a rotor has a position (expressed as an index with A=0) which determines at what point through its full rotation it is currently resting. This is used chiefly to calculate positional offsets, which we'll discuss is another step.
- **wiring**: A dictionary that maps an entry (right-hand) pin to an exit (left-hand) pin. Pins are numbered starting from A (from the rotor's frame of reference) as 1.
- **wiring_back**: A reversed dictionary of the same, used for computing the path of signals returning from the reflector.
- **ringstellung**: The Ring-Setting, which determines the position of the rotor's conception of A relative to its actual internal wiring. This was originally expressed as an index, like position. I've since changed the model to use a number, with A=1

In particular, the latter three points are crucial, and it all boils down to how you apply the ringstellung. Take a hypothetical wheel where A maps to N, and B maps to Q. With a ringstellung of A, (and assuming the wheel is in the A position), a signal of "A" passing into the wheel will exit at "N". If you instead apply a ringstellung of B, you have rotated all the wiring internally relative to the letter-ring on the wheelpack. A now follows B's path to Q, which lives under P (as it also moved back one letter).

Essentially, I had applied all these transforms backward additionally. Worse, I had also been shifting *only the contacts on the right hand side of the router*, due in large part to not understanding a debug-output diagram one other M4 emulator had provided. Once I realized the physical impossibility of rotating only the contacts on the right-hand side given the physical design of the rotor, I was able to correct that.

The transform is now done with the help of two dictionaries - one used to calculate the offset between an incoming signal at a neutral position and the corresponding pin it would hit on the entry face, and another to calculate the offset between another imaginary letter-wheel and the exit pins. For convenience, both dictionaries are used only while initializing the wheel - the ringstellung, once applied, is stored in the two wiring dictionaries and as a property of the rotor for reference, but the translational dictionaries are discarded.

Fixing the Ringstellung transforms turned out to be the final piece of the puzzle at 1.0.2dev81, and the code finally came back clean.

## Future Work
An updated test suite based on different known message plain-texts, for different initial conditions representing various known models of enigma, would be handy. There's also another toy project in my pipeline for which an interactive CLI front-end for Python_Enigma would be *enormously* valuable.

Though it was originally just a toy implementation, python-enigma's maintainance and ongoing feature development can be sponsored by backing ZAdamMac in the [GitHub Sponsors Program](https://github.com/sponsors/ZAdamMac). For the first year of the program, Github is matching your sponsorship pledges - so they're basically like half-off!