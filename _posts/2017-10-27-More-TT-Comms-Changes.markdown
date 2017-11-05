---
layout: post
title: "Complicating TT: Protocolizing Communications"
date: 2017-10-27 20:30 -400
categories: TarnishedTale
---

When I set out to create [Tarnished Tale](https://github.com/ZAdamMac/Tarnished-Tale) I wanted to keep things simple. I'm a fairly impatient person, and even when I'm chasing quality I try to take the most expedient path. That's why I used websockets and bcrypt rather than implementing my own communications protocol and cryptography. Well, that and it's a bad idea to roll your own crypto.

However, this week brought the decision to add an internal protocol to communications between a Tarnished Tale server and its clients. To a certain extent this was always intended. One of the factors that weighted the decision to use websockets was the idea that clients could be user-made and still be used to play (client validation would be implemented at some future date, and most important gameside functions happen serverside). Therefore a bit of protocol was always going to be needed, I just never thought to flesh it out.

A while back, I had been self-deprecating on Twitter about the lulziness of the then-current Test Client, which was literally code lifted from a websockets tutorial and highly deficient, when Amy Colford (github's own NurbsOtter) suggested she might slap something better together.

This suggestion tickled me - I've never worked collaboratively on a coding project before - so I said "go for it". Colour me surprised months later when she comes to me again and says not only did she do it, but she smoked the pants right off me with her React skills. Kudos with to anyone with the patience to use JS!

Anyway, we got to talking about the future of the client-server relationship and some of the features we both thought would make a MUD more playable for players of modern games (with surprising overlap of desired features), and the subject of protocolizing the communications came up. I described a ludicrously complex way to do it, and she, rightly, reminded me that JSON exists.

Once pushed, the next update to the server code makes all transmissions JSON objects. This will allow categorization by message type and make simpler some of the weirder requests that previously would have had to be pretty-printed.

It's also going to simplify the pretty-printing process, at least from the server side, as Nurbs is planning to implement prettyprinting on the client side, based on markdown. Excellent!
