---
author: Ed Zynda
categories:
- Javascript
- JQuery
- Tutorials
- Web Development
comments: true
date: 2013-11-27T00:00:00Z
share: true
tags:
- callback
- deferred
- javascript
- jquery
- promises
title: Javascript Promises Explained
url: /javascript-promises-explained/
---

While reading about different Javascript frameworks and utilities, I kept running across a design patter or technique called 'promises'. It seems every shiny new Javascript thing utilizes them in some way or another. At first the concept seemed difficult to wrap my head around but eventually it clicked. It isn't really as difficult as I thought.

Javascript as well as other modern languages uses a what are called callback functions. These are functions passed as arguments to other functions and are used to do something with the data that the top level function returns. You can pass a function name as the parameter like in this jquery example.

```javascript  
$( "#target" ).submit(myCallback);

myCallback = function ( event ) {  
    alert( "Handler for .submit() called." );  
    event.preventDefault();  
});  
```

Or you can use an anonymous function.

```javascript  
$( "#target" ).submit(function( event ) {  
    alert( "Handler for .submit() called." );  
    event.preventDefault();  
});  
```

This is all fine and dandy. A problem arises though when you start using various functions or features in Javascript that run asynchronously. That means they don't run in sequence but run concurrently with other functions. Take this small example.

```javascript  
setTimeout(function() {  
    // do some stuff  
    var myVar = 'Some value';  
}, 2000);

console.log(myVar);  
```

What happens when you run it? Well the setTimeout function takes a function as the first argument and a time in milliseconds as the second. The function passed as the first argument will then execute in the time you passed in the second. setTimeout itself is executed immediately though and then console.log right after that. Because of this myVar doesn't actually contain a value and you get an error. Two seconds later myVar is assigned 'Some value' but then it's too late. How do we access myVar only once our function has run? Well you can do this.

```javascript  
setTimeout(function() {  
    // do some stuff  
    var myVar = 'Some value';  
    console.log(myVar);  
}, 2000);  
```

By placing console.log inside our anonymous function we tell it only to execute when our function executes. Great! But this presents a problem. If you are writing a large application, it's possible you are going to have more than one function called within a function. You might even have multiple callbacks with even more nested functions. This can get messy. To solve this problem, members of the Javascript community came up with the idea of <a href="http://wiki.commonjs.org/wiki/Promises/A" title="Promises/A Specification" target="_blank">promises</a>.

Promises are a specification only and it is left up to developers to implement it. JQuery has it's own implementation of promises. A promise is just an object. This object is returned by a function instead of an actual value. It's basically an IOU for any other part of your script saying it will give you an actual value when it's done doing what it's doing.

JQuery uses what it calls a 'deferred' object. A deferred has a resolve message that is run once your promise is fulfilled. It also has a reject method that is run if an error or something occurs. Take a look at this example.

```javascript  
var myFunc = function() {  
    var deferred = new $.Deferred();  
    setTimeout(function() {  
        // do some stuff  
        var myVar = 'Some value';  
        deferred.resolve();  
    }, 2000);  
    return deferred.promise();  
};

myFunc().done(function() {  
    console.log('Finished: ' + myVar);  
});  
```

We've wrapped everything in a function called myFunc and created a new Deferred object. Our return value is a promise which belongs to Deferred. Now we can take that returned promise and use a method called 'done'. 'done' is only executed when the Deferred object is resolved or when the 'resolve' method is called. There are two more methods called 'fail' and 'always'. Fail is executed when the 'reject' method is called. You can probably guess when the 'always' method is executed. There is a fourth method called 'then' which takes three functions as arguments. These functions would be the same that you would use for 'done', 'fail' and 'always' in that order.

That's only the basics of what you can do with promises but even that is pretty powerful.

 [1]: http://www.edzynda.com/media/promise.jpg
