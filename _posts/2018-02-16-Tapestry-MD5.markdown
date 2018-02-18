---
layout: post
title: "Tapestry's Test Hash: MP5‽ Really?"
date: 2018-02-16 20:30 -400
categories: Tapestry
---

## Tapestry Is Getting Real

Real ProgramsTM use some form of automated testing - the ideallic standard being unit testing - and since I'm intending to turn Tapestry into something like a polished and useful offering, that means it should to. Starting with the upcoming v1.0 release, all future Tapestry builds will have been subjected to an automated functional testing program.

The current version of the test script involves a step called the Identity Test - comparison of the recovered version of the files contained in a backup with those found in the original test corpus.

The best way to do that, of course, is with hash comparisons.

## Hashing for Dummies
A hash function is a one-way cryptographic operation that creates some manner of (ideally) unique representation of some other form of data.

In the testing script's case, this is done for each matching pair of files (ie, `Control/Corpus/Docs/1` and `Test/Corpus/Docs/1`) using a hashing algorithm called MD5.

## What's up with MD5?
There's a special attack against hashing algorithms called a collision - the idea being to generate two distinct files that would hash to the same value. As it happens, [an MD5 collision has been known for some time](https://www.mathstat.dal.ca/~selinger/md5collision/). MD5 has been considered insecure for years, and some high-security builds of GPG and Python both exclude MD5 as an available algorithm for that reason.

But in this case, these hashes aren't being used for a purpose related to security - merely as an expedient way to compare two fairly-large files to one another. While it's possible to flummox this, it wouldn't happen accidentially. MD5 is therefore sufficiently accurate for the purpose of the testing.

## Why Settle for Less?
Resources and speed. MD5 is a lightweight hashing algorithm. It doesn't use many resources, which is ideal since the testing operation involves about 80 such hashes.

If MD5 will do the job, there's no good reason to use a more-secure, but slower option like SHA-256.

*If you like Tapestry or any other Patch Savage Labs project, please consider tipping our [coffee fund](https://www.ko-fi.com/PSavLabs). Proceeds go to lab improvements and, to be fair, coffee.*