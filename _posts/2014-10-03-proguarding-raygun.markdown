---
layout: post
title: "Raygun and Proguard: There and Back Again"
date: 2014-10-03 13:41:00 +0100
comments: true
categories: [Java, Android, Development, Proguard, Raygun]
---
I've recently had some fun trying to make Raygun and Proguard play nicely in one of our Android application so I think it might help some people.
If you know what each of these tools do, just skip to the Problem section, otherwise, keep reading.

Note: the examples I'll be using are going to be Android specific, yet can easily be used in a pure Java project.

##What is Proguard and does it go well with bacon?

Java is an awesome language. Fast, stable, runs anywhere, and runs *everywhere*.

It is also almost as reverse engineerable as Javascript, due to it being compiled to byte code, rather than machine language of ones and zeros.
Essentially we are leaving our classes, methods and class variables visible for all world to see. With little effort you can modify some code or replace it with something else and misuse it like that  (think replacing your proprietary ```boolean isLicenseValid() ``` method to always return ```true```).

If we are distributing executables to end users, as in for example desktop Java or Android apps we want our apps to be as solid as possible and cause anyone attempting to reverse-engineer them as much of a hard time as possible. (Java apps running as the backend of web applications aren't affected as you're not distributing executables - they are running only on servers you control).

[Proguard](http://proguard.sourceforge.net/) is an utility that does that - it obfuscates Java apps (also Android) and also strips out the unused methods (of external libraries), debug logs, and makes your app smaller and safer.

Essentially it takes your ```com.example.package.MyClass.myMethod()``` and turns it into something like ```a.b.c.D.e()```.
Obviously, this makes it harder to reverse engineer, but also quite impossible to debug when you find something like that in the stacktrace.
Luckily, Proguard generates a mapping file - *mapping.txt* which describes the steps taken to rename any names. The entries look like this, for myMethod in MyClass.
```
com.example.package.MyClass -> a.b.c.D:
 int myMethod(java.lang.String) -> e
```

It's also very easy to run and add to your build scripts, in Gradle it looks something like this, to run it as part of a release build:
```
buildTypes {
    release {
        runProguard true
        proguardFiles 'proguard-rules.txt'
    ...
```
Alternatively, you can run it from shell as well (or use the GUI if that's your thing).

**Proguard won't run becase XYZ**

See that *proguard-rules.txt* file there? That's where you tell proguard what to do, and more importantly, what to keep.
You will want to keep you annotations, 3rd party libraries, and you model classes that get serialized, to JSON for instance. There's where all the magic happens and where [Stack Overflow](http://stackoverflow.com/search?q=proguard) comes in handy so I won't go into detail here. Esentially you need a custom-built rules file for each project.

##And wtf is [Raygun](http://raygun.io)?
It's simply the best crash-reporting tool ever. A while ago I blogged about using it with Actionscript, and this time we're using it for Android.
Here you have the benefit of it reporting *all* crashes, not just the ones where user chooses to report it. All of them, straight up there, straight to your inbox, nagging you for writing shit code.

Which is the right thing to do.

##Getting Raygun and Proguard play nicely together

Remember proguard-rules.txt Raygun is one of these 3rd party libraries; If you don't include the following lines in your proguard rules file, your raygun won't make any successful POST requests as the request will be incorrectly formatted.

You should add these lines to your proguard rules:
```
-keep class main.java.com.mindscapehq.android.raygun4android.** { *; }
-keepnames class main.java.com.mindscapehq.android.raygun4android.*
-keepclassmembers class main.java.com.mindscapehq.android.raygun4android.* { *; }
```

Basically: Without these lines, Proguard would obfuscate everything in the Raygun4Android (or Raygun4Java) library, including the models used to serialize your exceptions and zap requests to Raygun.io, and instead of receiving something like:

```
{
    'occurredOn': ...,
    'details':{
        'machineName':...
        ...
```
You are effectively sending something like this instead:
```
{
    'a': ...,
    'b':{
        'c':...
        ...
```
And Raygun will throw you 400 responses because your requests don't mean anything.

**My errors are obfuscated!**

Yes. Raygun will now *accept* your requests, but your stack traces will still contain obfuscated code.
So your NullPointerExceptions will look like:

```
NullPointerException: null
    a.a.b.Ca.b in SourceFile:170
    ...
```
Making it hard to understand anything, again.
Luckily Proguard, when doing the obfuscation, also generates a mapping file - *mapping.txt*. This file contains all class, variable, method names and how they got renamed, so you can trace the names back to their original ones using that file. The relevant mapping would be something like this:

```
com.example.package.MyClass -> a.a.b.Ca:
 int myMethod(java.lang.String) -> b
```
So we know that in class that maps to a.a.b.Ca is a method b that is called at line 170 and that is actually a com.example.package.MyClass and method myMethod(String).

That's how you decode errors from Proguard.

But that sucks. Manually going through a mapping.txt file that can easily exceed a Meg is just a drag. That's why the kind Proguard people have created a nifty script called *retrace* that does it automagically.

```
./retrace.sh path/to/mapping.txt path/to/stacktrace.txt
```
is how you call it and it converts the stacktrace to what is was like pre-obfuscation.

But.

Raygun people decided to display their stacktraces in a *humanly* readable format, which is all fine and dandy yet not *proguard-readable*. Essentially, proguard requires stacktraces to have standard Java formatting, whereas these don't have it.

Hence I wrote a [quick and dirty script](https://github.com/DigitalInnovation/raygun_proguard) that will format your stacktrace from Raygun's JSON responses. Needs only Ruby to run and takes 2 parameters.

```
ruby stack_construct.rb [raygun.json file] [output.file stacktrace]
```
The output file is properly formatted for Proguard and can then be thrown into the previously mentioned script for de-obfuscation.

That's pretty much it.

Also - I have it on my TODO, but if you find it really useful you can help by making it automatically deobfuscate with the output of the script to make it even better to use. Or scrape Raygun's website. Or if you're from Mindscape just let me download the original stacktrace when an error is reported. That would be swell. Until then.
