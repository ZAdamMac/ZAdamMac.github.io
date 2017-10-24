---
layout: post
title:  "TarnishedTale: Overview"
date:   2017-05-22 14:00:00 -0300
categories: TarnishedTale
---

It all started as a half-joking statement on a Discord channel. I remarked that I found the typical aspects of penetration testing in games to be lacking - this followed on after a conversation about my incredibly stupid "Virtual Access Control Lab" build I did in Minecraft. (It wasn't finished. No link for you.) I joked that the sorts of games that always had the most promise for being the corporate iSpy types were all either boring as hell, or glossed over the interesting aspects of breaking and entering.

I joked:
> Someone should make a pentesting roguelike. I know python, I could do a MUD!

Alright, so this was a blanketly stupid statement. Nobody should make a pentesting game, it'd be even more horrible if it was a roguelike, I barely know python, and I will eventually one day be able to say I built a MUD, but it is not this day.

The idea kept banging around in my head though, and I eventually came up with something that resembled a product structure. It's going to look something like this:

### TarnishedTale Client
- Parse Protocol
- Asynchronous read/transmit from server
- More features as plugins (automapping, paned view, etc etc.)

### TarnishedTale Server
- Adjudicate games based on the TarnishedTale ruleset.
- Handle chat based on in-game location, as well as global channels and user-to-user.
- Authenticate users based on traditional access levels
- Parse Player Races with the Race module
- Parse Player Statistics using the TarnishedTale ruleset.
- Roguelike functionality
- Highly configurable
- Maps, Items, NPCs, and Players stored externally of the main code

It turned out virtually none of these things were easy. Not that they were unsolved problems in computing - MUDs have existed longer than the phrase "MMORPG" stands for.

As it happens I already started work on TarnishedTale before this post. I built a very rudimentary echo-server and a client using Python2.7 with `asynchat`. Then I tried to implement TLS (I'm a security nerd, so of course I did), which fouled everything past my ability to troubleshoot. I'm now beginning a rewrite in `twisted`.

## But things have reasons!
Yes, they do. Let's go through some of them. For starters, as mentioned, MUD engines exist everywhere. I wanted a custom one to make it easier for me to use as an operator, partly because it would implement the rules I intend to use by default.

### Why a custom protocol?
Because I intend to host my particular TarnishedTale instance (game information forthcoming) directly, from my home network, and I want to limit the overall use of bandwidth. The protocol was designed around minimizing the amount of data transfered between the Client and Server, in both directions, by reducing some whole "screens" to error codes. This protocol exists somewhere between what the player will input, and what TCP sees. A whole post - probably several - could explain this protocol, so I won't go into it much further than that.

### Why Python?
Because I know it, and it's sufficient. As we go forward I might use something more common or appropos for the client, granted that Python isn't the easiest for GUI development. That remains to be seen.

### Why Plugins for the Client?
Because I used to play MUDs, and a huge barrier to entry for my friends I'd try to get to play with me was the text-only interface. Yes, everyone (at the time) had telnet, but not everyone had the patience to manually draw maps and so on. Using plugins or a paned view will help get around that.

### Why a custom game system?
Instead of D20, you mean? A few reasons, not the least of which was I was trying to build the game as generally as possible. D20 accels at this in some ways, but I had no intention of ever using Classes, which are a pretty core part of the d20 system. I also wanted to avoid legal entanglements. I think the TarnishedTale system is a fairly reasonable balance of human-parseable and computable, so I look forward to giving it a real play-test.

### Why such a complicated chat system?
It's basically expected in this, the age of the graphical MMO. I was a WoW player back when I didn't have better things to do, and I recall a rather elaborate environment of communication. You had several local chats, basically set up on a radius-basis around your character, plus global chat channels (some player-defined, some established by the system, like Trade), plus private "whisper" chat, plus guild channels, and so on and so forth. Emulating some of this would be an interesting practice but I feel also something that would be highly desired by potential players AND game admins.

### Why are you going to overcomplicate user authentication?
Because it's 2017 and people break shit for fun now. Also because the game server will represent an open port on my home network and I'd very much like to minimize the amount of pivoting you can do from it.

### Why seperate the races engine as a module?
Because not all games would call for it.

### Why rougelike?
It is my desire to allow game admins to set particular maps (be they a dungeon, the wilderness, etc) as roguelike, as desired. I feel that the challenge of randomly generating a playable map would be interestingly complex and a nice diversion from the more serious work of network management.

### Why externalize the database?
Well, for one, Python is terrible at databases, and I'm not sure that's the right term. The saved files will probably look like `configparse` files that get loaded into instances of classes made for the purpose.

More relevantly, storing the game data outside of the actual game engine/server code facilitates the end goal of a re-usable set of code. TarnishedTale in its final form will be useful to anyone hoping to build a MUD.

### Will the project be open source?
Yes. Both for the Client and the Server, and for the final game. The license will allow server operators to collect donations to defray hosting costs, but I'm leery of allowing anyone to monitize it, including myself.

### What is the current state of the project?
Lord knows. At time of writing the project had stalled and needed a complete rewrite with a reliance on a different module.
