---
layout: post
title: "Has Alix finished yet?"
date: 2014-04-13 13:05:28 +0100
comments: true
categories: [hack, random]
---

My friend, Alix is running London Marathon today.
I am a lazy arse so I decided to do a sprint instead. I am in fact, too lazy to
do a proper sprint sprint, as in running, sweating, outside kind of sprint, so I
decided to do it behind a monitor.

So I made an app to track her progress. It can be found at:
[http://has-alix-finished-yet.herokuapp.com/](http://has-alix-finished-yet.herokuapp.com/)

The whole thing is pretty simple; Take [Kimono](http://www.kimonolabs.com/) to generate an API, parsing the
[London Marathon website](http://www.virginmoneylondonmarathon.com/), specifically
the page tracking her.

The API looks something like this:

```
{
  "name": "hasAlixFinishedYet",
  "count": 1,
  "frequency": "realtime",
  "version": 2,
  "newdata": false,
  "lastrunstatus": "success",
  "lastsuccess": "Sun Apr 13 2014 10:18:37 GMT+0000 (UTC)",
  "results": {
    "collection1": [
      {
        "finish_time_name": "Finish time *",
        "finish_time_of_day": "14:57:42",
        "finish_total_time": "04:41:01"
      }
    ]
  }
}
```

Then write a few lines of (fugly) ruby code, like this:

```
require "sinatra"
require "net/http"
require "uri"
require "json"

get '/' do

  uri = URI.parse("http://www.kimonolabs.com/api/e7k515ks?apikey=7c5b3f77b85342910964f336ccb3de15")
  resp = Net::HTTP.get_response(uri)
  data = resp.body

  object = JSON.parse(data)
  results = object["results"]
  puts results
  resultsCollection = results["collection1"]
  puts resultsCollection

  finishTimeName = resultsCollection[0]["finish_time_name"]
  finishTimeOfDay = resultsCollection[0]["finish_time_of_day"]
  finishTotalTime = resultsCollection[0]["finish_total_time"]

  puts finishTimeName

  if finishTimeName.include? "*"
    "No. But should be at #{finishTimeOfDay}"
  else
    "Yes! She finished at #{finishTimeOfDay} in #{finishTotalTime}"
  end

end
```

Set up a Rack application:

```
require './hello'
run Sinatra::Application
```

Then take [Heroku](heroku.com) to host it.
Job done.
