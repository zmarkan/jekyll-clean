---
layout: post
title: "Android Versioning Hell in the CI-land"
date: 2014-05-07 19:50:28 +0100
comments: true
categories: [Android, Development, Gradle]
---

For the last two days, my colleague Paul and I had been battling with a weird
issue in our Android test suite running on CI. We had been getting *Unable to
find instrumentation info* gradle errors whilst running the connectedCheck task.

Suckers looked like this:

```
"Tests on Nexus 5 - 4.4 failed: Unable to find instrumentation info for:
ComponentInfo{com.example.app.test/android.test.InstrumentationTestRunner}".
```

Digging around the logcat I found this:

```
W/PackageManager(  608): Can't install update of com.example.app.test
update version 0 is older than installed version 3
```

and realised that by porting an old Eclipse project into Android Studio we
introduced the following into our *build.gradle*:

```
defaultConfig {
    minSdkVersion 8
    targetSdkVersion 19
    versionCode 1
    versionName "1.0"
}
```

Thing is that gradle android plugin will *create* a new AndroidManifest.xml file
(well more than one, one for each build flavour), and it'll do it from the existing
AndroidManifest.xml and its own parameters, such as *defaultConfig*. This *AndroidFrankenfest.xml*
is then used in constructing the .apk files we all know and love, but funnily enough,
removing the *versionCode* and *versionName* lines from *build.gradle* rolled back
to using the ones in the original manifest. Fun, but confusing.

We also figured out that the bloody app wasn't getting uninstalled automagically,
so Paul ended up writing a new rake task (yes we use Rake and so should you, but
  that's a whole new story) for that, to ensure that the bloody app was getting uninstalled,
both the .test package, such as: *com.digitalinnovation.android.app.test*,
as well as the app package.

Runs like a charm.
