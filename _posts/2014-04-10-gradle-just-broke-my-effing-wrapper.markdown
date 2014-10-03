---
layout: post
title: "Gradle just broke my effing CI and here is how to fix it"
date: 2014-04-10 10:33:00 +0100
comments: true
categories: [android, gradle]
---

###Update:
Gradle have fixed the http download link, so any changes you might have made are futile.
Sorry! 

Something curious happened this morning.
Travis was throwing gradle errors, more accurately, *gradle* was throwing *zip*
errors.

If your gradle wrapper is throwing weird zip errors, it's because gradle has changed
their download urls.

Errors look like this:

```
Serenity:MNSRemoteAB-Android zan$ ./gradlew clean
Unzipping /Users/zan/.gradle/wrapper/dists/gradle-1.11-all/7qd8qq8te5j4f5q9aaei3gh3lj/gradle-1.11-all.zip to /Users/zan/.gradle/wrapper/dists/gradle-1.11-all/7qd8qq8te5j4f5q9aaei3gh3lj
Exception in thread "main" java.lang.RuntimeException: java.util.zip.ZipException: error in opening zip file
	at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:78)
	at org.gradle.wrapper.Install.createDist(Install.java:47)
	at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:129)
	at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:48)
Caused by: java.util.zip.ZipException: error in opening zip file
	at java.util.zip.ZipFile.open(Native Method)
	at java.util.zip.ZipFile.<init>(ZipFile.java:220)
	at java.util.zip.ZipFile.<init>(ZipFile.java:150)
	at java.util.zip.ZipFile.<init>(ZipFile.java:164)
	at org.gradle.wrapper.Install.unzip(Install.java:160)
	at org.gradle.wrapper.Install.access$400(Install.java:29)
	at org.gradle.wrapper.Install$1.call(Install.java:70)
	at org.gradle.wrapper.Install$1.call(Install.java:47)
	at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:65)
	... 3 more
```

###tl;dr:
*Go to your gradle/wrapper/gradle-wrapper.properties file, and change the
distributionUrl to: https\://services.gradle.org/distributions/gradle-1.11-all.zip*

###Explanation
On a clean checkout, gradle wrapper in 1.11 hits the http address -
http://services.gradle.org/distributions/gradle-1.11-all.zip, whereas the download
link on the website points to the https version.

Hacking around, I discovered the file being downloaded was empty and the website
link worked perfectly.

Hope this helps.
