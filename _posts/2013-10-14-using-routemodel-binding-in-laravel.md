---
title: Using Route/Model Binding In Laravel
author: Ed Zynda
layout: post
permalink: /using-routemodel-binding-in-laravel/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Laravel
  - Tips
  - Tutorials
  - Web Development
tags:
  - binding
  - laravel
  - models
  - PHP
  - routes
---
As I work more and more with Laravel, I keep running across some really cool time saving tricks. One of those time savers is route/model binding.

<!--more-->

As I work more and more with Laravel, I keep running across some really cool time saving tricks. One of those time savers is route/model binding.

Let&#8217;s say that you have the following route setup.

<pre lang="php">Route::get('books/{book}, function($book) {
    return Book::find($book);
});
</pre>

That&#8217;s pretty simple right? Basically you point your browser at /books and then pass a book id and then use the &#8216;Book&#8217; model to fetch that book from the database. But say we already decided that the &#8216;books&#8217; route should always be working with the book model and a book should be fetched automatically. Laravel gives us a method for explicitly binding the &#8216;Book&#8217; model to our routes.<pre lang="php> Route::model('book', 'Book'); Route::get('books/{book}, function($book) { return $book; }); </pre> 

Now Laravel will take the id you pass to the route and automatically fetch it behind the scenes. What if we want to fetch a book by something other than it&#8217;s id? What if we want to access our books using a URI similar to /books/BookTitle? Instead of Route::model we can use Route::bind like this.

<pre lang="php">Route::bind('book', function($value, $route) {
    return Book::whereTitle($value);
});
</pre>

This is a really good way to cut down on lines of code and at the same time keep it more readable.