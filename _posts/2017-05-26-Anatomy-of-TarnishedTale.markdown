---
layout: post
title:  "Anatomy of TarnishedTale"
date:   2017-05-26 12:00:00 -0300
categories: TarnishedTale
---

If you're wondering why TarnishedTale doesn't have its own repository like Tapestry does, the principle reason is that it doesn't yet work, in any sense of the word (at present, it actively throws errors when you attempt to launch the server at all.) In lieu of that, and because debugging is a little too frustrating to do *before* work, I thought I'd take the time to talk a bit about how it's intended to work.

## Layer One: Communications
At its core, TarnishedTale uses Twisted, a python module designed to make network application development easier, which takes a lot of the guesswork out of that area. A major stumbling block in development had been trying to get Transport Layer Security (TLS) properly implemented over asynchat, another python module I was using to handle the communications side of the server and client code. The Collection thread is the main thread of the script - it listens on the operator-configured port for TLS-based connections, adjudicates a database that correlates users to their response addresses, and sorts the incoming messages to the appropriate handling queues.

### Why use TLS?
I'm a security nerd. In reality, I chose to use TLS because it solves an otherwise tricky problem - how to secure communications between the server and *individual* clients. I don't want just anyone to be able to sniff a TarnishedTale host and capture the passwords of every user trying to log in. TLS provides a layer of encryption, and also a layer of certainty that you are connecting to the server you wanted to connect to.

### What about the TLS Certificates?
I'm still thinking about this. It was tempting to create for myself a Certificate Authority (CA) specifically for validating TarnishedTale hosts, but that's its own complex ball of wax. The DevBuild is using a self-signed certificate that I have specifically included acceptance for in the client code, but **only** if the address of the server is on `localhost`.

### What's that about sorting the inputs?
The Collections thread is recieving what are essentially `POST` requests from the client, admittedly with the message block written in a custom protocol. Because of the multithreaded design of TT, these messages can be parsed by the collections thread and written into the queue for one of the actual handling threads. It's my hope that allowing multiple threads in the server will help reduce the percieved processing time client-side to a point where the main source of latency in the system is down to communications delay over the internet connections involved.

The sorting is done by parsing out the first "word" of the user input and determining from that what sort of command is being invoked. `dictCommands` provides a "use category" for all the commands the game knows how to process. The message and the user who sent it are made a tuple and dropped into the corresponding queue. The current conceptualization uses threads for Chat, Combat, Rooms, Players, and SystemRequests. It is intended to have some rooms include operator-defined commands, so the current concept also dumps commands with no known category into Rooms, where they are either executed or your standard "I don't know how to do that" or "I see no X here" responses are generated.

## Layer Two: World Rendering
At its core, any game is basically a database, but this is particularly true of Text Adventure games. I have attempted to avoid the complicated process of re-implementing SQL by using classes. Specifically, I have created classes called `playerLoader`, `mobLoader`, and `roomLoader`. All rely on the `ConfigParser` module.

### Rendering Players
If you're at all used to playing tabletop games you will immediately recognize playerLoader and the corresponding save files as character sheets. A new instance of the class is created every time an action that needs the character sheet is executed - chiefly through the Combat, Room, and Player threads - then the sheet is saved and dropped upon completion of the task in question. Such saves contain all the game-rule data needed to model an active character, as well as player-defined values like the short and long character descriptions, character name, and so forth. This class also calls to the items database for EQUIP commands.

### Rendering Rooms - and Correlating Maps
The `roomLoader` class handles a lot of data and operates similarly to `the playerLoader`. It tracks the contents of the room, its description, which npcs are present, and how many exits there are. If necessary it will call to the main items DB to look up what is necessary for USE commands related to those items. Movement is handled, after a fashion, by roomLoader, because the room's individual save tells you what other rooms - in what other maps - it tracks to.

#### Directory Structure of Maps and Rooms
A lot of how individual maps and rooms operate is actually handled in the directory structure, rather than in code. All maps live in `~/world`. A map (whether parsed as a region, city, dungeon, house, or so on) has its own directory, and that directory includes individual ConfigParser files for every room in that map. It may also include one of three other files `modworld`, `rogueworld`, and `templates`. The presence of `modworld` restricts the access of the whole map to players who are on the moderator or operator whitelists. `Rogueworld` and `templates` turn that map into a roguelike map

#### Roguelike Maps
Roguelike maps are regenerated when *all the players with access to the map* have died in that map - these players are stored in the `rogueworld` file. If this happens, the `SYSTEM` thread will handle regenerating the map in the background in the appropriate spot in the queue. I haven't quite implemented the algorithm that will test a randomly-generated map for overall playability.

### Rendering Mobs
This works much like the playerLoader, but mob/NPCs are persistent instances called up from templates, and have to be kept in memory, so the call is a little different. The mobLoader class also has to invoke some simple AI tasks - speech, combat, and movement. More complicated NPCs - such as a concept I have had for **Automatic Demigods**, would actually be bots with special player saves giving them special powers.

## Layer Three: Functional Operation
### The Chat Thread
A thread dedicated to the chat system manages the lists of global channel audiences (determining who to send which messages to when posted into global channels) as well as resolving requests for the Say, Shout, and Whisper commands, which function about as you would expect (say is room-wide, shout is map-wide, and whisper is player-to-player).

### The System Thread
System handles a lot of background management - freeing up "dead" sockets, kicking AFK players, "unsticking" class instances that are no longer needed, and processing roguelike-regeneration requests. The system thread also handles LOGIN and QUIT requests from clients, and adjudicates most mod/operator commands.

### The Rooms Thread
The room thread is what handles a lot of the player experience - it's probably the busiest thread, and might actually require some internal multithreading or several constituent worker threads when implemented at scale. It's intended to handle the reading and sending of rooms. It adjudicates all non-combat skill checks. It adjudicates movement between invidual rooms, and for certain commands would even need to load multiple rooms and their contents at once. Both player and NPC movement requests are handled in the Rooms Thread.

#### Operator-Defined Commands and the Room Thread
Individual rooms may have a specific command idiosyncratic to them. The precise mechanism for including this in the save file isn't yet clear, but the importance of this feature in preventing bloat for the main code, and facilitating customization of the game for the operator, is important enough that I am going to priorize the development of this feature.

### The Combat Thread
The combat thread adjudicates the combat system - loading the relevant players and NPCs and resolving their attacks against each other.

#### How does TT handle combat?
I'm still deciding, mostly because it's going to come down to playtesting tweeks. At the moment, I'm thinking every player and mob have certain speed values. Everyone present for a combat (everyone in the same room this combat is occuring in) enters commands within a fairly generous window, and then once a full "stack" of combat commands is obtained, they are resolved in an order consistent with that speed value. This provides some of the fairness of a turn-based system, by eliminating macro-to-win strategies, without the relative complexity of a global cooldown.

#### Is PVP included?
Yes, but the specific mode of PVP and any penalties that may be applied to PVP are fuzzy at the moment. I personally have never had a great appetite for PVP in games and I've sort of left the issue on the back burner.

### The Players Thread
A simple thread that adjudicates requests for the player's character sheet and skill advancement.

#### Why is TarnishedTale Classless?
I intended to make the game as open-ended and customizable as possible - a TarnishedTale server should be as useful to the developer of a game set in any setting as I can possibly make it. To facilitate this I've done away with classes for the Operator to edit. This also offers up options for players as well.

#### How does skill advancement work?
Players accumulate experience in individual skills as they are used, as is done in Bethesda or Chaosium games. Accumulating a certain level of experience will result in a certain amount of increase in the corresponding skill's score.

#### What about player attributes?
Derived scores - such as HP and MP, are rooted in the player's "physical" attributes. These can be increased in a number of ways through the use of items (both temporarily and permanently) or through spells (if included) and NPCs (if there are any that have those functions). As well, the player has an "overall level" determined by the sum of all their individual experience scores, advancement in which merits advancement in physical attributes as well.

#### You say "if included" for spells. How is magic going to work?
Magic will be fundamentally a skill. Players will gain opportunities to learn individual spells based on their overall magic skill. Whether this is through items or opportunities in rooms is up to the discretion of a developer.

In Apotheosis, the main game being developed under TarnishedTale, players will have opportunities to join one or more specialized magic "schools" with their own unique spell lists.

Spells, like most other aspects of the game, are completely operator-definable.

## Part Four: Talking Back
Each thread, individually, completes each task it is assigned by sending a protocol-correct response back to the player whose request it was adjudicating. Waiting for access to the port will likely be the main point at which the interpreter switches between threads.
