---
layout: post
title: "MNSRemoteAB - AB Testing library for Android"
date: 2014-04-10 11:44:31 +0100
comments: true
categories: [android, AB Testing]
---

AB testing can be a bitch. There's many different platforms and solutions to
choose from, each with their own benefits.

At M&S we decided to roll out our own solutions, so we will be able to AB test our
applications with one configuration file for all three platforms:

- [Android](https://github.com/DigitalInnovation/MNSRemoteAB-Android)
- [iOS](https://github.com/DigitalInnovation/MNSRemoteAB-iOS)
- [Windows Phone](https://github.com/DigitalInnovation/MNSRemoteAB-WP8)

##How does it work

###Configuration

There is a single config file on interwebz somewhere with a JSON configuration.
Our example one can be found [here](http://mnsremoteab-config.herokuapp.com/remoteconfig).

The schema looks something like this:

```
{
  "sections": [
    {
      "section_name": "Section1",
      "last_modified": "2014-04-10T06:08:48+00:00",
      "options": [
        {
          "option_name": "ChoiceA",
          "weight": 25,
          "udid": [],
          "dictionary": [
            {
              "key": "value",
              "value": "A"
            }
          ]
        },
        {
          "option_name": "ChoiceB",
          "weight": 25,
          "udid": [
            "1234",
            "56789"
          ],
          "dictionary": [
            {
              "key": "value",
              "value": "B"
            }
          ]
        },
        ...

```

The root element, *Sections*, has objects with a *last_modified* attribute,
and an array of *options*.

The options are given *weight*, or probability, the sum of which in all options
in a single section must be 100.

They also have a *dictionary*, which is an array of *key/value* pairs.
It specifies the choices that one option holds. They can be font sizes,
font faces, colours, positions, whatever you fancy.

###Usage

I'm going to go into the details of the Android library here, its other platform
counterparts are going to be published on their separate blogs.

As of now, the only way to use it is inside your own gradle project. It builds with
gradle 1.11, the latest version of Android build tools and SDK 4.4.2.
We are, however, about to publish it to Sonatype maven repository for easier inclusion
in projects.

We're going to be looking at the [*DemoActivity*](https://github.com/DigitalInnovation/MNSRemoteAB-Android/blob/feature/DemoApplication/app/src/main/java/com/mands/di/mnsremoteab_demo/app/DemoActivity.java) in the *demo* module.

First off, in the *onCreate* method, we instantiate our library with path to our
remote configuration file, like this:

```
_mnsRemoteAB = new MNSRemoteAB(getApplicationContext(), "http://mnsremoteab-config.herokuapp.com/remoteconfig");
```

(Note that, ideally you would have _mnsRemoteAB instantiate in Application class,
not your main activity. )

```
_mnsRemoteAB.initialize(this);
```
Then, we can call the *initialize* method, passing in an instance of *OnInitializedListener*.
That is, in our case, the *DemoActivity* itself.
This gives us a callback:

```
@Override
    public void initialized(boolean isSuccessful) {

        setupButtonLayout();
    }
```
the *isSuccessful* means that our RemoteConfig has been loaded, parsed, resolved
and stored successfully.
Now we can call the *setupButtonLayout()* method that applies the AB resolved options:

```
private void setupButtonLayout() {

        TextView section1Key1Value = (TextView) findViewById(R.id.Section1Key1Value);
        section1Key1Value.setText(_mnsRemoteAB.getKeyValue("Section1.value", "Failed"));

        TextView section2Key1Value = (TextView) findViewById(R.id.Section2Key1Value);
        section2Key1Value.setText(_mnsRemoteAB.getKeyValue("Section2.value", "Failed"));
```

Getting a value is done by calling *_mnsRemoteAB.getKeyValue*, where the first parameter
consists of *Section* DOT *key*, and the second one is the default value to return
in case of failure.

Even if the *isSuccessful* in the *initialized* callback was false, you could call
the *getKeyValue*, getting the default values back.

That's a quick example of usage, this will be covered in Readme file as well,
and further explanation on how it works will follow here in the coming days.

Happy testing!
