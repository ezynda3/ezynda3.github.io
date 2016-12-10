---
author: Ed Zynda
categories:
- Laravel
- Tutorials
- Web Development
comments: true
date: 2013-09-27T00:00:00Z
share: true
tags:
- GD
- image manipulation
- laravel
- PHP
title: Simple Image Manipulation With Intervention Image
url: /simple-image-manipulation-with-intervention-image/
---

A lot of web applications include some sort of photo uploading functionality. Whether it&#8217;s allowing a user to upload a personal avatar or to create and manage an entire photo gallery with watermarking features, it&#8217;s somewhat of a pain to code. PHP includes several built-in functions for accomplishing this using the GD library but they&#8217;re cumbersome at best. Luckily someone else feels your pain and has written a class that makes it a snap to work with images. &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; is written by <a title="Oliver Vogel" href="http://olivervogel.net/" target="_blank">Oliver Vogel</a> and it integrates brilliantly with our favorite framework Laravel!

Installation like anything that uses Composer is a snap. Add this to the &#8220;require&#8221; section of your composer.json file.

```json
{
    "require": {
        "intervention/image": "dev-master"
    }
}
```

Run

```bash  
$ composer install  
```

To add use it in Laravel open the &#8216;config/app.php&#8217; file. Add this to the $providers array.

```php
<?php
'Intervention\Image\ImageServiceProvider'
```

Next add this to the $aliases array.

```php
<?php
'Image' => 'Intervention\Image\Facades\Image'
```

This will allow you to reference the class as &#8216;Image&#8217; and use all of it&#8217;s static methods.

One great feature of the &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; class is that it&#8217;s methods are chainable just like most Laravel classes. Uploading, resizing and saving an image to an new location can be accomplished in a single line.

```php  
<?php
// resizing an uploaded file
Image::make(Input::file('photo')->getRealPath())->resize(300, 200)->save('foo.jpg');
```

For more information on &#8216;<a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">Intervention\Image</a>&#8216; and how it can be used, check out the <a title="Intervention\Image" href="http://intervention.olivervogel.net/" target="_blank">official website</a>.

 [1]: http://www.edzynda.com/media/image_manipulation.png
