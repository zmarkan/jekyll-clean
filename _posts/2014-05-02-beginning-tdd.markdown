---
layout: post
title: "Beginning TDD"
date: 2014-05-02 07:30:31 +0100
comments: true
categories: [TDD, Development]
---

Last Wednesday, my colleagues [@Hetal_Patel](https://twitter.com/Hetal_Patel) and [@cpatuzzo](https://twitter.com/cpatuzzo)
and myself ran a Hands-On session with the same name at the
[London Software Craftsmanship Community](http://www.meetup.com/london-software-craftsmanship/).

![image](/images/beginning_tdd.jpg)

First off, big thanks to [@SamirTalwar](https://twitter.com/SamirTalwar) and
[@roberttaylor426](https://twitter.com/roberttaylor426) and everyone else who helped
us pull this off, including Pivotal Labs London office for hosting us and providing burritos.

Idea was to present TDD to people with little or no experience in testing or TDD.
We first started doing a series of internal TDD workshops or 'Katas' for our
colleagues at M&S after last
[Global Day Of Coderetreat](http://globalday.coderetreat.org/).

## How it went

We made some [slides](http://slides.com/chrispatuzzo/beginning-tdd) and a [Github
Repo](https://github.com/zmarkan/StringCalculatorKata) with projects stubs for
String Calculator for Java, .NET and Ruby. Basically they are only empty projects
with the first test.

(Yet, somehow I *still* managed to fuck up the Eclipse project by foolishly thinking
I can just export it from IntelliJ. Well, we live and learn and next time I'll make
a .pom. :) )

After a short intro on TDD and Red-Green-Refactor principle,
we did a show of hands to see what language should we live-code
the FizzBuzz example in (people laughed at the mention of FizzBuzz).
We learned that practically no-one uses .NET, where Java and Ruby
were kinda sorta there-there so we ended up with Chris showing off his mad ruby/vim skillz.

Then attendees were told to pair up, select their weapon of choice (Some people
  chose PHP because why the fuck not, TDD is just a tool and language doesn't
  matter one single bit).

We then spent a good chunk of time going around and giving tips on how to make the
easiest possible tests and make them pass in an easiest possible way.

###When do we refactor?

The hardest things seem to be writing the simplest tests possible and implementing
them in the lazies possible manner. (You want me to return zero, I'll give you your damn zero)

Also, *when do we refactor* is a very good question. Frankly, it's a gut feeling.
In general I tend to refactor early rather than late, after a couple repetitions emerge,
but then that depends on my familiarity with the subject and number of edge cases.
IMO doing the edge cases is cool as they help you box your problem for our puny
human brains to process easier.

###Pub, DHH, and TDD is dead.
Funny enough, we held the session a day or two after
[DHH decided to kill it](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html).
(DHH is the guy who made Rails).
Luckily enough, Kent Beck (the guy who invented TDD) [made a fun and good point on why
DHH is not quite dead yet](https://www.facebook.com/notes/kent-beck/rip-tdd/750840194948847),
giving us all plenty of discussion topics.
