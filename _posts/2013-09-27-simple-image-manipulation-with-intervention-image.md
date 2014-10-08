---
title: Simple Image Manipulation With Intervention Image
author: Ed Zynda
layout: post
permalink: /simple-image-manipulation-with-intervention-image/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Laravel
  - Tutorials
  - Web Development
tags:
  - GD
  - image manipulation
  - laravel
  - PHP
---
[<img class="alignnone size-medium wp-image-490" alt="image_manipulation" src="http://www.edzynda.com/media/image_manipulation-300x219.png" width="300" height="219" />][1]

A lot of web applications include some sort of photo uploading functionality. Whether it&#8217;s allowing a user to upload a personal avatar or to create and manage an entire photo gallery with watermarking features, it&#8217;s somewhat of a pain to code. PHP includes several built-in functions for accomplishing this using the GD library but they&#8217;re cumbersome at best. Luckily someone else feels your pain and has written a class that makes it a snap to work with images. &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; is written by <a title="Oliver Vogel" href="http://olivervogel.net/" target="_blank">Oliver Vogel</a> and it integrates brilliantly with our favorite framework Laravel!

<!--more-->

[<img class="alignnone size-medium wp-image-490" alt="image_manipulation" src="http://www.edzynda.com/media/image_manipulation-300x219.png" width="300" height="219" />][1]

A lot of web applications include some sort of photo uploading functionality. Whether it&#8217;s allowing a user to upload a personal avatar or to create and manage an entire photo gallery with watermarking features, it&#8217;s somewhat of a pain to code. PHP includes several built-in functions for accomplishing this using the GD library but they&#8217;re cumbersome at best. Luckily someone else feels your pain and has written a class that makes it a snap to work with images. &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; is written by <a title="Oliver Vogel" href="http://olivervogel.net/" target="_blank">Oliver Vogel</a> and it integrates brilliantly with our favorite framework Laravel!

Installation like anything that uses Composer is a snap. Add this to the &#8220;require&#8221; section of your composer.json file.

[js]  
&#8220;intervention/image&#8221;: &#8220;dev-master&#8221;  
[/js]

Run

[sh]  
$ composer install  
[/sh]

To add use it in Laravel open the &#8216;config/app.php&#8217; file. Add this to the $providers array.

[php]  
&#8216;Intervention\Image\ImageServiceProvider&#8217;  
[/php]

Next add this to the $aliases array.

[php]  
&#8216;Image&#8217; => &#8216;Intervention\Image\Facades\Image&#8217;  
[/php]

This will allow you to reference the class as &#8216;Image&#8217; and use all of it&#8217;s static methods.

One great feature of the &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; class is that it&#8217;s methods are chainable just like most Laravel classes. Uploading, resizing and saving an image to an new location can be accomplished in a single line.

[php]  
<?php<br /> // resizing an uploaded file

  
Image::make(Input::file('photo')->getRealPath())->resize(300, 200)->save(&#8216;foo.jpg&#8217;);  
[/php]

For more information on &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; and how it can be used, check out the <a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">official website</a>.

 [1]: http://www.edzynda.com/media/image_manipulation.png