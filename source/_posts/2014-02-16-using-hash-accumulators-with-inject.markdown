---
layout: post
title: "Ruby: using .inject with a hash accumulator"
date: 2014-02-16 19:29:43 -0600
comments: true
categories:
---

Earlier this week we were tasked with calculating the most and least popular colors from an array of cars. So, given this array:

```ruby
@cars = []
3.times { @cars << Car.new('black') }
5.times { @cars << Car.new('silver') }
2.times { @cars << Car.new('white') }
```

We needed to make methods that would return the most popular color (5 silver cars) and the least popular (2 white cars). The first thing I did was make a new array from the array of car objects (using the .map method) populated with just colors. Like this:

```ruby
@colors = @cars.map { |car| car.color }
```
If you puts that array it would look like this:
```ruby
["black", "black", "black", 'silver', 'silver', 'silver', 'silver', 'silver', "white", "white"]
```

So, now how do you get a count of all items in that array, incremented every time you hit a duplicate?

.inject

First, let's talk about .inject. Ruby docs says .inject:
>Combines all elements of enum by applying a binary operation, specified by a block or a symbol that names a method or operator.
>
>If you specify a block, then for each element in enum the block is passed an accumulator value and the element. At the end of the iteration, the final value of memo is the return value for the method.

whaahuhhh?

Well, let's break it down by using the classic example of .inject. To obtain a sum of each element in an array you could do this:
```ruby
[1,2,3,4,5].inject(0) { |item, element| item + element } # => 15
```
The argument you pass into <code>.inject(0)</code> is then passed into the <code>item</code> variable, which is then added to the first <code>element</code> in the array. Then the return value of the block is passed back into the block as <code>item</code>. This is done for each element in the array, leaving you with a sum of the numbers.

So, back to our array of colors. How do I turn that into <code>{"black" => 3, "silver" => 5, "white" => 2}</code>?

Well, if I use <code>Hash.new(0)</code> as the first accumulator value, that will make a new hash and the default values for each key you pass in will be 0. Then, everytime I pass a duplicate into that hash, I just increment the value for that given key, then return the entire hash back into the accumulator. Like this:

```ruby
@frequency = @colors.inject(Hash.new(0)) do |hash, key|
    hash[key] += 1
    hash
    end
```

So, for each element of the @colors array, we inject a hash with the element becoming the key, and everytime that key is passed into the block, we increment the value. Voila!
```
{"black" => 3, "silver" => 5, "white" => 2}
```
Now I can access the lowest and/or highest values in the array using the <code>min_by</code> or <code>max_by</code> methods for a hash.

Until next time...

>If you end up with a boring miserable life because you listened to your mom, your dad, your teacher, your priest, or some guy on television telling you how to do your shit, then you deserve it.
>
> \- Frank Zappa




