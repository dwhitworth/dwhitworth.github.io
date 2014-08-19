---
layout: post
title: "Blocks, Procs, Lambdas &amp; Closures in Ruby"
date: 2014-04-20 14:03:16 -0500
comments: true
categories: [JavaScript]
---

In JavaScript, we are quite used to the functional features of the language and creating closures. In Ruby, we do it all the time, but sometimes don't realize that we are creating closures and passing functions around.

<!-- more -->
Take our very common .map method in Ruby:

```ruby

array = [1,2,3,4,5] # => [1,2,3,4,5]

array.map { |x| x * x } => [1,4,9,16,25]

```
We know that when we pass a block into the .map method it will return a new array based on the data in the original array provided and the code we put in the block. But, what's going on internally?

Well, really all that's going on is our good, old .each method, with a yield to the block, then pushing those values into the new array. Let's try to rebuild the .map (we'll call it new_map) method from scratch:

```ruby
class Array

  def new_map(&block)
    new_array = []

    self.each do |item|
      new_array << yield(item)
    end

    new_array

  end

end
```
What makes the above an example of a closure? Well, a closure is basically a method(or function) that has a couple of properties:
- It can be passed around, like any other object.
- It remembers all the values of the variable it had when it was defined. So, even though they may no longer be in scope, the method still has access to those variables.

Let's do a simple closure example using a Proc:

```ruby
def add_one
  x = 0
  Proc.new { x += 1 }
end

a = add_one
a.call # => 1
a.call # => 2

b = add_one
b.call # => 1
```

In the above example, the add_one method has a return value of a Proc that adds 1 to x, which was defined outside the Proc, but inside the method. So, your Proc has access to x, because it was available when that Proc was defined.

Well, what's a lambda then and what makes it different than a Proc?

First, let's see the syntax of Procs, vs Lambdas:

```ruby

a = Proc.new { |x| x * x} # this is a Proc that will return x^2 when called

b = lambda { |x| x * x } # this is a lambda that will return x^2 when called

c = ->(x) { x * x } # another way to define a lambda that will return x^2 when called
```

Here, we have defined a proc and a couple of lambdas, very similar to how we would define anonymous functions in JavaScript. What's the difference between procs and lambdas? Not much. If you call .class on a lambda, you will find that they are just procs. The only differences is that lambdas are a more strict form of a proc:
- Lambdas are more strict with argument checking. With procs you can pass in the wrong number of arguments and will still execute, while lambdas will throw an error.
- Lambdas can return a value with the <code>return</code> keyword.

So, what's the point of using closures? In a pure functional language, there's a HUGE point to it. Functional languages are *stateless*, but closures are how you can make some state continue as long as the closure continues. In imperative languages like Ruby, the purpose is a little bit cloudier, but you can use lambdas and procs sometimes to make your code a bit more concise in certain situations.

Bis später.
