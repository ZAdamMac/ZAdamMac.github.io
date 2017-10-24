---
layout: post
title:  "TT Security: Adding Output Sanitization"
date:   2017-10-24 12:00:00 -0400
categories: TarnishedTale
---

[Tarnished Tale](https://www.github.com/ZAdamMac/Tarnished-Tale) isn't merely just a vanity project or a distraction. When I said in the readme and prospectus that one of the objectives was to develop a secure MUD engine, I meant it.

For me, security is a package deal. There's more to security than preventing Little Bobby Tables from smashing the game databases or skids giving themselves Infinity Money in game. It's about Alice and Bob enjoying their game in peace without needing to worry too much about Eve poking her nose in where she doesn't belong... but Eve isn't the only part of the threat model.

Those who read source, or alternatively, those who had to listen to me get way too involved in this yesterday, know that TT leverages a bit of HTML5 in its pretty-printing function, `roomFormat`. This is all well and good, and if the only html ever sent to clients came from code that was put there by myself or a GameOp we'd have no problems.

Unfortunately, we're talking about a somewhat fancier websockets chat engine, so that's not the case. Using one of the chat functions or (really) any other user-populatable field, a player can provide code of any text-representable kind they like that TT will happily pass on to other players.

## Why didn't this seem like a problem at first?
To be fair, injection attacks are nothing new. XKCD ran the now-famous Little Bobby Tables strip back when I was still in College, if not maybe High School. When I realized I was going to have to swap the `users.db` config file with a proper SQL database I became quite concerned with them. Fortunately, python's default sql handler, `sqlite3` was designed with this in mind and handles input sanitization relatively efficiently when used properly.

It didn't become clear to me until much later - when I was setting up `roomFormat`, that the websocket between client and server was literally passing code back and forth, and then the client would execute it, without question.

## Isn't this mostly a cosmetic problem?
I'm not gonna lie - I was hoping to do the minimum possible with hyperlinks and images, which was to say nothing in the base game - and hope nobody else did either. But HTML isn't the only thing you can pass over a websocket and have parsed - you can push literally anything the client browser would execute, and the browser dutifully will.

## What's the risk?
Anything you can inject with javascript and probably still more issues after that. I'm picturing players hiding coinhive mining JS in their character descriptions or hiding some kind of tracking cookie or any other browser hijack in their character names.

## There must be a solution!
There is. Since I have to allow player-returned code to be visible to other players (both for the chat system and for character descriptions and suchlike) and intend to have an online room editor, the solution has to be more elegant than "drop all code". Fortunately there's a tool for that in `html5lib` which sanitizes input just fine. My solution is to have all transmission out from the server pass through the sanitizer code first, just to make sure.

## Why not sanitize the *inputs*?
Because that's a half-solution. I also want to prevent gameops from hiding code in room descriptions and other places they themselves can write to. If the admin running the server and its corresponding page wants to bully everyone into mining Etherium for them, that's their business, and they can do it from outside the game's framework directly.
