---
layout: post
title: "SQL Is Your Friend"
date: 2014-04-26 16:36:52 -0700
comments: true
categories:
---

Our final project for MakerSquare was crowd-funding platform for public dares, challenges and acts of kindness. You can check it out here: [we-dare-you.herokuapp.com](http://we-dare-you.herokuapp.com). Every dare had multiple monetary contributions and we would sum up those contributions via a "pot_size" method. <!-- more -->

When summing up multiple elements in an array, our go to method is Ruby's <code>inject</code> method, but sometimes we forget that SQL has some built in functionality that may be faster and ease the load on memory.

We had 8 models with quite a few associations, but let's concentrate on two models:

```ruby
class Dare < ActiveRecord::Base
  has_many :contributions
  # ...
end

class Contribution < ActiveRecord::Base
  belongs_to :dare
  # ...
end
```

So, to get a pot size for a specific dare, we made a method that looked something like this:

```ruby
def pot_size
  self.contributions.inject(0) {| total, contribution | total + contribution.amount }.to_f / 100
end
```

This should be refactored to:

```ruby
def pot_size
  self.contributions.map(&:amount).inject(:+).to_f / 100
end
```

Using this method, we use the has_many association to get all the contributions for a specific dare. The SQL generated looks something like this:

<code>SELECT "amount".* FROM "contributions" WHERE "contributions"."dare_id" = <dare_id></code>

I seeded a database with 100,000 test contributions and the above method took 1.6 seconds and change. The SQL statement above is finding all the contributions and putting them into memory before adding up the amounts.

A much more efficient way is to use SQL to do the work of summing the amounts. It will be much faster and use much less memory. We can refactor the "pot_size" method to look more like this:

```ruby
def pot_size
  self.contributions.sum(:amount).to_f / 100
end
```

It generated SQL that looked like this:

<code>SELECT SUM("contributions"."amount") AS sum_id FROM "contributions" WHERE "contributions"."dare_id" = <dare_id></code>

Using the above method, we avoid pulling all those records into memory and let SQL sum up the amounts in one step. The above method took 17ms!

The moral of the story is you don't *always* have to use Ruby methods when manipulating your ActiveRecord objects, sometimes good old SQL is faster and uses less memory.

