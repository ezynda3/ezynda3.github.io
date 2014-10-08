---
title: Javascript Promises Explained
author: Ed Zynda
layout: post
permalink: /javascript-promises-explained/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Javascript
  - JQuery
  - Tutorials
  - Web Development
tags:
  - callback
  - deferred
  - javascript
  - jquery
  - promises
---
[<img src="http://www.edzynda.com/media/promise-300x202.jpg" alt="Promise" width="300" height="202" class="alignnone size-medium wp-image-595" />][1]  
While reading about different Javascript frameworks and utilities, I kept running across a design patter or technique called &#8216;promises&#8217;. It seems every shiny new Javascript thing utilizes them in some way or another. At first the concept seemed difficult to wrap my head around but eventually it clicked. It isn&#8217;t really as difficult as I thought.

<!--more-->

[<img src="http://www.edzynda.com/media/promise-300x202.jpg" alt="Promise" width="300" height="202" class="alignnone size-medium wp-image-595" />][1]  
While reading about different Javascript frameworks and utilities, I kept running across a design patter or technique called &#8216;promises&#8217;. It seems every shiny new Javascript thing utilizes them in some way or another. At first the concept seemed difficult to wrap my head around but eventually it clicked. It isn&#8217;t really as difficult as I thought.

Javascript as well as other modern languages uses a what are called callback functions. These are functions passed as arguments to other functions and are used to do something with the data that the top level function returns. You can pass a function name as the parameter like in this jquery example.

[js]  
$( &#8220;#target&#8221; ).submit(myCallback);

myCallback = function ( event ) {  
alert( &#8220;Handler for .submit() called.&#8221; );  
event.preventDefault();  
});  
[/js]

Or you can use an anonymous function.

[js]  
$( &#8220;#target&#8221; ).submit(function( event ) {  
alert( &#8220;Handler for .submit() called.&#8221; );  
event.preventDefault();  
});  
[/js]

This is all fine and dandy. A problem arises though when you start using various functions or features in Javascript that run asynchronously. That means they don&#8217;t run in sequence but run concurrently with other functions. Take this small example.

[js]  
setTimeout(function() {  
// do some stuff  
var myVar = &#8216;Some value&#8217;;  
}, 2000);

console.log(myVar);  
[/js]

What happens when you run it? Well the setTimeout function takes a function as the first argument and a time in milliseconds as the second. The function passed as the first argument will then execute in the time you passed in the second. setTimeout itself is executed immediately though and then console.log right after that. Because of this myVar doesn&#8217;t actually contain a value and you get an error. Two seconds later myVar is assigned &#8216;Some value&#8217; but then it&#8217;s too late. How do we access myVar only once our function has run? Well you can do this.

[js]  
setTimeout(function() {  
// do some stuff  
var myVar = &#8216;Some value&#8217;;  
console.log(myVar);  
}, 2000);  
[/js]

By placing console.log inside our anonymous function we tell it only to execute when our function executes. Great! But this presents a problem. If you are writing a large application, it&#8217;s possible you are going to have more than one function called within a function. You might even have multiple callbacks with even more nested functions. This can get messy. To solve this problem, members of the Javascript community came up with the idea of <a href="http://wiki.commonjs.org/wiki/Promises/A" title="Promises/A Specification" target="_blank">promises</a>.

Promises are a specification only and it is left up to developers to implement it. JQuery has it&#8217;s own implementation of promises. A promise is just an object. This object is returned by a function instead of an actual value. It&#8217;s basically an IOU for any other part of your script saying it will give you an actual value when it&#8217;s done doing what it&#8217;s doing.

JQuery uses what it calls a &#8216;deferred&#8217; object. A deferred has a resolve message that is run once your promise is fulfilled. It also has a reject method that is run if an error or something occurs. Take a look at this example.

[js]  
var myFunc = function() {  
var deferred = new $.Deferred();  
setTimeout(function() {  
// do some stuff  
var myVar = &#8216;Some value&#8217;;  
deferred.resolve();  
}, 2000);  
return deferred.promise();  
};

myFunc().done(function() {  
console.log(&#8216;Finished: &#8216; + myVar);  
});  
[/js]

We&#8217;ve wrapped everything in a function called myFunc and created a new Deferred object. Our return value is a promise which belongs to Deferred. Now we can take that returned promise and use a method called &#8216;done&#8217;. &#8216;done&#8217; is only executed when the Deferred object is resolved or when the &#8216;resolve&#8217; method is called. There are two more methods called &#8216;fail&#8217; and &#8216;always&#8217;. Fail is executed when the &#8216;reject&#8217; method is called. You can probably guess when the &#8216;always&#8217; method is executed. There is a fourth method called &#8216;then&#8217; which takes three functions as arguments. These functions would be the same that you would use for &#8216;done&#8217;, &#8216;fail&#8217; and &#8216;always&#8217; in that order.

That&#8217;s only the basics of what you can do with promises but even that is pretty powerful.

 [1]: http://www.edzynda.com/media/promise.jpg