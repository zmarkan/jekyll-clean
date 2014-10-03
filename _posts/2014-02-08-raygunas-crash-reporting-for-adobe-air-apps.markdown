---
layout: post
title: "RaygunAS - the best crash reporting library for Adobe Air apps in the 'Verse"
date: 2014-02-08 08:26:35 +0000
comments: true
categories: [Actionscript, Raygun, open source, Adobe Air]
---

Actionscript is a nice little language, I find it great to code in. Relying on Adobe isn't smart, as you can figure out just by looking at the fragmented state of their documentation and support blogs. 

Crash reporting is especially painful, as there were no tools enabling that. Until now.

Enter [RaygunAS](https://github.com/DigitalInnovation/raygunAS), a plugin for [Raygun.io](www.raygun.io) that enables us to do just that.
 

So, how do I do this?
---------------------

All you need is to create an account with Raygun (they have a free trial available for 60 days, just click on the Get Started link on their website).

Name your first app and get the API Key. Save it close to your heart.


### Building and Testing

Clone the repository and build the *.swc* file. 
Tests are there so if you don't trust me (you shouldn't, really) run them.
  
I used IntelliJ 13 for building but I see no reason why Flash Builder or any other tool wouldn't be able to build it. 
  
If all is fine and dandy copy the *.swc* file to wherever you have your libs and all is set.


###Usage

Then go to your app and put the following lines in the main Sprite class, after some initial setup is complete, but before the main drawing begins: 

 	_raygunAs = new RaygunAS(this,RAYGUN_API_KEY, APP_VERSION);
 	_raygunAs.addEventListener(RaygunAS.READY_TO_ZAP, onRaygunReady);
        _raygunAs.chargeRaygun();
    }

    private function onRaygunReady( event:Event ):void
    {
        //do logic here
    }
    
 We use the [Starling engine](http://gamua.com/starling/) in our app and I put these just before initializing Starling, but anywhere should do, just initialize your main in the *onRaygunReady* callback. 
 
That's that for the setup, and now if you create an Error, let's say that you forget to initialize a variable somewhere in your code, Raygun will fire up and zap the error to your app's dashboard.

You can also attach a *RaygunAs.RAYGUN_COMPLETE* listener to your RaygunAs instance in order to catch those error. 
It handles [*UncaughtErrorEvent.UNCAUGHT_ERROR*](http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/events/UncaughtErrorEvent.html) events.

###Reporting

What you get in your Raygun.io dashboard (along with a friendly email telling you you fucked up somewhere), are the following:

 - stacktrace of said error,
 - device model and OS Version 
 - resolution,
 - device orientation, 
 - and your app's version. 
 
 
###Contributing

This is marked as socialist-friendly software on [Github](https://github.com/DigitalInnovation/raygunAS), so feel free to fix or improve it by issuing a pull request.

Happy Zapping!


Update
------

The kind folks at Raygun featured this project on their website, among the [Third-party providers](http://raygun.io/raygun-providers/)!

Cheers! 
    
 




