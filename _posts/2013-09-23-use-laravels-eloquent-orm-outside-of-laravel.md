---
title: 'Use Laravel&#8217;s Eloquent ORM Outside Of Laravel'
author: Ed Zynda
layout: post
permalink: /use-laravels-eloquent-orm-outside-of-laravel/
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dm3sb_custom_sidebar:
  - 
categories:
  - Laravel
  - Tutorials
  - Web Development
tags:
  - Composer
  - database
  - Eloquent
  - laravel
  - ORM
  - PHP
---
[<img class="alignright size-medium wp-image-484" alt="ORM" src="http://www.edzynda.com/media/orm-247x300.jpg" width="247" height="300" />][1]

Working with databases in Laravel is ridiculously easy thanks to Eloquent. Eloquent allows you to map database tables as simple model objects that include various methods for retrieving and updating the database. While working on other people&#8217;s code, however, I&#8217;m usually not lucky enough to be working with something as enjoyable as Laravel. Thankfully Laravel is written in such a way that some of it&#8217;s key components can be used as stand-alone modules.

<!--more-->

[<img class="alignright size-medium wp-image-484" alt="ORM" src="http://www.edzynda.com/media/orm-247x300.jpg" width="247" height="300" />][1]

Working with databases in Laravel is ridiculously easy thanks to Eloquent. Eloquent allows you to map database tables as simple model objects that include various methods for retrieving and updating the database. While working on other people&#8217;s code, however, I&#8217;m usually not lucky enough to be working with something as enjoyable as Laravel. Thankfully Laravel is written in such a way that some of it&#8217;s key components can be used as stand-alone modules.

Using Laravel&#8217;s database magic in your own projects is pretty simple. First you need to make sure you have all the required components included in your project. Currently Laravel 4 is the latest version and it&#8217;s codename is &#8220;Illuminate&#8221;. It&#8217;s database component is therefore called &#8220;Illuminate\Database&#8221;. The easiest way to grab &#8220;Illuminate\Database&#8221; and it&#8217;s dependencies is to use <a title="Composer" href="http://getcomposer.org/" target="_blank">Composer</a>.

Make sure there is file called &#8216;composer.json&#8217; in your project that contains the following:  
[js]  
{  
&#8220;require&#8221;: {  
&#8220;illuminate/database&#8221;: &#8220;*&#8221;  
}  
}  
[/js]  
Now run:  
[sh]  
composer install  
[/sh]  
In order to start using Eloquent let&#8217;s create a file called database.php to include in our various project files.  
[php]  
<?php<br /> require 'vendor/autoload.php'; 

use Illuminate\Database\Capsule\Manager as Capsule; 

$capsule = new Capsule; 

$capsule->addConnection(array(  
&#8216;driver&#8217; => &#8216;mysql&#8217;,  
&#8216;host&#8217; => &#8216;localhost&#8217;,  
&#8216;database&#8217; => &#8216;test&#8217;,  
&#8216;username&#8217; => &#8216;test&#8217;,  
&#8216;password&#8217; => &#8216;l4m3p455w0rd!&#8217;,  
&#8216;charset&#8217; => &#8216;utf8&#8242;,  
&#8216;collation&#8217; => &#8216;utf8\_unicode\_ci&#8217;,  
&#8216;prefix&#8217; => &#8221;  
));

$capsule->bootEloquent();  
[/php]  
Now we can start working with database tables just like we would if we were using Laravel!  
[php]  
<?php<br /> include 'database.php'; 

// Create the Books model  
class Books extends Illuminate\Database\Eloquent\Model {  
public $timestamps = false;  
}

// Grab a book with an id of 1  
$book = Books::find(1); 

// Change some stuff  
$book->name = &#8220;The Best Book in the World&#8221;;  
$book->author = &#8220;Ed Zynda&#8221;;

// Save it to the database  
$book->save();  
[/php]  
Check out the Laravel <a title="Laravel Eloquent documentation" href="http://laravel.com/docs/eloquent" target="_blank">documentation</a> for more information on working with Eloquent.

 [1]: http://www.edzynda.com/media/orm.jpg