---
layout: post
title: "Tapestry 2.0's Development: A Postmortem"
date: 2019-04-17 18:00 -300
categories: Tapestry
---

Tapestry 2.0 was released yesterday, and while I slapped together a quick release on the topic at the time, I wanted to follow along that idea with a "lessons learned" retrospective on our testing and development process.

[Tapestry](https://kenshosec.com/Projects/tapestry.html) is a Free, Open-Source Software tool Kensho Security Labs is developing to automate the process of making Zero Storage Trust Backup records, and recovering from those records. We're currently looking for contributors, so check out [the github repo](https://github.com/ZAdamMac/Tapestry).

## What Went Right
I'm going to preface this with what went right. It will be a relatively short preface.

### Planning Saves the Day
While the timeline went completely out the window, the truth of the matter is that by and large, the plan for how to implement and the actual result of the implementation was more or less 1:1. Developing Tapestry 2.0 started with a relatively extensive planning and conceptualization phase that laid the groundwork for every line of code, commit, and other action which followed.

**What could have been done better**: We'll cover most of this later, but somehow, a plan was made to create the tasks necessary for the Integrity Checking functions, but not to implement the behaviour which calls those tasks. This was missed until after the release. Obviously we'll need more robustness of planning. This will probably be done by using nested project boards with one master board tracking sub-projects to handle design and implementation phases seperately.

## What We Learned: Test More (And Less)
As much as things went well, the final testing process of Tapestry 2.0 took ages and sapped a lot of gumption that it frankly shouldn't have. Part of this was due to the clunky nature of the testing, and part of it was due to the testing cadence - doing all your tests at the *end* of a complete rewrite is... suboptimal.

### True Unit Testing Shouldn't Require Runtime Tests
Most of Tapestry's testing in terms of time-spend are two sets of tests: `runtime-tests.py`, which takes about 45-50 minutes on my machine, and `integrity-tests.py` for which runtime-tests.py is a dependancy and then also takes about another ten minutes on top of it.

Very little of tapestry is unit tested. Part of this was due to limitations in my thinking and skillset - not knowing a sane way to mock out files, and having no concept of Continuous Integration.

Moving forward, before any features can be added to Tapestry, a test suite redesign will need to be pursued. Runtime tests should be necessary at release time only and should be a formality, since moving forward, unit test coverage will be much higher and executed in such a way as to give a strong assurance that the runtime tests at least "should" pass.

### Functional Testing Needs to Happen More
Along the same vein, the testing cadence needs to change. Waiting until the end of a development cycle to begin testing is a bad idea for two reasons:
1. It fosters the risk of initiating on a bad premise and then building out the whole release on that bad premise, and;
2. The delay caused by final testing and debugging is a major gumption trap and should therefore be avoided.

If we had unit tests that could be run on a per-commit or per-push basis we could have avoided a lot of the headaches of the testing process, which lasted a month longer than the anticipated week. This also means that individual functions would get tested and debugged rather than entire releases at once.

### More Proof-of-Concepts!
A big part of the testing delay was the Windows Testing, made necessary after 2.0.0 re-introduced windows functionality. This caused delays for a number of reasons - firstly, because flawed concepts sent us back to the drawing board for experimentation, and that meant the linux tests that had been passed previously could have been avoided.

This complication would have been minimized if more time was spent in windows testing proofs-of-concept for various implementation designs.

## How you can help?
If you like Tapestry and the direction it's heading from, we'd like to hear from you. Unlike many other forms of software, Tapestry currently has absolutely no telemetry of any kind built in. We're not even sure how many other people have used it!

If you have suggestions for or problems with Tapestry, please let us know at [the Tapestry inbox](mailto:tapestry@kenshosec.com). If you're also a developer, particularly in Python, feel free to check out [the github repo](https://github.com/zadammac/Tapestry), submit a PR, answer questions, or even just give your thoughts and feedback.

We've kept the cost of Tapestry as low as possible, and I refuse to make the script itself nagware, or change the license to require payment. That said, if you wanted to contribute to the project financially, our best avenue is via the Kensho Security Labs Coffee Fund, located [here](https://ko-fi.com/kenshosec).

Follow my twitter, [@zadammac](https://twitter.com/zadammac) for the upcoming announcement of the associated mailing list for announcements regarding Tapestry, including update releases.
