---
layout: post
title: "Javascript closures (hopefully) explained"
date: 2014-03-02 21:35:38 -0600
comments: true
categories: "JavaScript"
---
I started going through a fairly thorough Javascript assessment on Friday. <!-- more -->It's now Sunday night and I have gotten through 36 of the 51 challenges so far. The last challenge I completed was on closures, and it was a doozy. So, in an effort to understand the concept better, I going to do my best to confuse erm... explain them to you and how they might be used practically.

When we declare local variables in a function in Javascript, after the function is run, those variables vanish. Take this example:

```javascript
var myFunction = function (){
  var x = "superawesomelocalvariable";
  console.log("I love my " + x);
};

myFunction();
```

In the above example, after myFunction is run, we have no access to the variable x. Ok, now let's see a simple closure:

```javascript
var createAnAwesomeFunction = function(){
  var x = "superawesomelocalvariable";
  function innerFunction(){
    console.log("I love my " + x);
  };
  return innerFunction;
};

var myFunction = createAnAwesomeFunction();
myFunction();
```

So, in the above example, it's going to do the same thing as the first example. But, it's kind of counter-intuitive. The variable x should be gone, since createAnAwesomeFunction has already been run, and it only returns innerFunction. We receive a log to the console of "I love my superawesomelocalvariable" because myFunction has become a closure. A closure not only includes a function, but the entire environment that was available (including the variable x) when the closure was created. Pretty neat, eh?

Ok, so how do we actually use this in the real world?

Let's use a jQuery example:

```javascript
function divResize(size) {
  return function() {
    $(this).css('height', size + 'px');
  };
}

var div100 = divResize(100);
var div200 = divResize(200);
var div300 = divResize(300);

$('.makeme100').on('click', div100);
$('.makeme200').on('click', div200);
$('.makeme300').on('click', div300);
```

With this html:

```html
<html>
  <head>
  </head>
  <body>
    <style>
    .makeme100 {
      background-color: blue;
    }
    .makeme200 {
      background-color: purple;
    }
    .makeme300 {
      background-color: orange;
    }
    </style>

    <div class="makeme100">
      Make me 100 pixels high!
    </div>
    <div class="makeme200">
      Make me 200 pixels high!
    </div>
    <div class="makeme300">
      Make me 300 pixels high!
    </div>

    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
  </body>
</html>
```
When those divs are clicked, they will resize to their expected results. All thanks to a Javascript closure.

Until next time.
