---
layout: post
title: "Ruby: The absolutely 100% wrong way to define a method"
date: 2014-08-18 21:44:51 -0700
comments: true
categories: [Ruby, JSON, Active Model Serializers, APIs, Rails]
---

The app that I am building and maintaining contains travel and geographical information for a bunch of destinations around the World. I was tasked to build an API that would serve some JSON info from our database to an iOS app requesting it. Simple enough. In the database that I created everything was pretty simply and logically named (so I thought), but I knew ahead of time that there was going to be some data transformation, so I decide to implement [ActiveModel::Serializers](https://github.com/rails-api/active_model_serializers) for easy encapsulation and customization of the JSON object that I was delivering via said API.

Without changing anything, my app would deliver a JSON object that looked something like this:

<code>{ "id": 1, "description": "This is a description", "latitude": "33.9425° N", longitude: "33.9425° N" }</code>

<!-- more -->

Surely, the names couldn't be that different in the iOS client that needed the data? Even so, it's pretty easy to transform data in ActiveModel::Serializer by defining custom attributes. Say you wanted to change the name of "description" to "info" in your JSON, you would just do this in your serializer:
``` ruby
class AreaSerializer < ActiveModel::Serializer
  attributes :info

  def info
    object.description
  end

end
```
Easy, right? Well, whoever created the iOS app decided that they wanted the JSON object to look something like this:

<code>
{ "Area info page (400 characters)": "This is a description", "center latitude": "33.9425° N", "center longitude": "33.9425° N" }

It couldn't be changed on the iOS side, as that app didn't have any full-time Objective-C devs maintaining it, so the, ummm..., flexibility had to be on my end. Well, not only is that naming convention completely strange and eff'd up, how do I make a method name with spaces in it (also completely wrong and eff'd up)? Basically I need the above code to look like this (which doesn't work, btw):

``` ruby
class AreaSerializer < ActiveModel::Serializer
  attributes :"Area info page (400 characters)"

  def Area info page (400 characters)
    object.description
  end

end
```

One of the reasons I love Ruby (and one of the reasons you should be really careful, since Ruby lets you get away with shit like this)... define_method! Here's how you make really awful looking method names (kids, don't try this at home):

``` ruby
class AreaSerializer < ActiveModel::Serializer
  attributes :"Area info page (400 characters)"

  define_method("Area info page (400 characters)") do
    object.description
  end

end
```
And there you go! If you need to write some really horrible looking method names in Ruby, that's how you do it. Until next time...
