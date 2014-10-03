---
layout: post
title: "Android Test/Deploy for Lazy People"
date: 2014-02-02 10:03:58 +0000
comments: true
categories: 
- Android
- Continuous Integration
- Testing
published: false
---

Android is a wonderful, operating system. It is also a sick, sick puppy when it comes to testability, deployability and all that.

The thing about these things is, I don't particularly like them, hate them in fact. That's why I want a machine to do them for me so I can spend my time doing more productive things, like building prototypes, drinking coffee and shooting juice bottles with nerf guns.


Problem
----
Where I do believe Android people are working hard on solving these problems, in the meantime we are stuck with compromise solutions found in a million places.

### Build Systems

When It comes to building Android software, you really have only two options. Either use Maven, that worked well so far for the OS community, or Gradle, a relative newcomer but with Google's backing.

I decided to bet on Gradle, as chances are that that one is only going to improve, where the other one is going to be phased out.

Also, gradle uses Maven's dependencies so... how hard could it be really?

*Silly me.*

Gradle comes packed with Android Studio, the new IntelliJ-based IDE for Android, again with Google's backing. 
That one has many other goodies baked in, but most importantly, superb Gradle integration on a project-level, so that Gradle is doing your building for you, not their own background software. 


