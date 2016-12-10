---
author: Ed Zynda
categories:
- Laravel
- Tutorials
- Web Development
comments: true
date: 2013-09-23T00:00:00Z
share: true
tags:
- Composer
- database
- Eloquent
- laravel
- ORM
- PHP
title: Use Laravel&#8217;s Eloquent ORM Outside Of Laravel
url: /use-laravels-eloquent-orm-outside-of-laravel/
---

Working with databases in Laravel is ridiculously easy thanks to Eloquent. Eloquent allows you to map database tables as simple model objects that include various methods for retrieving and updating the database. While working on other people&#8217;s code, however, I&#8217;m usually not lucky enough to be working with something as enjoyable as Laravel. Thankfully Laravel is written in such a way that some of it&#8217;s key components can be used as stand-alone modules.

Using Laravel&#8217;s database magic in your own projects is pretty simple. First you need to make sure you have all the required components included in your project. Currently Laravel 4 is the latest version and it&#8217;s codename is &#8220;Illuminate&#8221;. It&#8217;s database component is therefore called &#8220;Illuminate\Database&#8221;. The easiest way to grab &#8220;Illuminate\Database&#8221; and it&#8217;s dependencies is to use <a title="Composer" href="http://getcomposer.org/" target="_blank">Composer</a>.

Make sure there is file called &#8216;composer.json&#8217; in your project that contains the following:  

```json  
{
    "require": {
        "illuminate/database": "*"
    }
}
```

Now run:  

```bash  
$ composer install  
```  
In order to start using Eloquent let&#8217;s create a file called database.php to include in our various project files.  

```php  
<?php  
require 'vendor/autoload.php';  

use Illuminate\Database\Capsule\Manager as Capsule;  

$capsule = new Capsule;

$capsule->addConnection(array(
    'driver'    => 'mysql',
    'host'      => 'localhost',
    'database'  => 'test',
    'username'  => 'test',
    'password'  => 'l4m3p455w0rd!',
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
    'prefix'    => ''
));

$capsule->bootEloquent();
```

Now we can start working with database tables just like we would if we were using Laravel!  

```php  
<?php
include 'database.php';

// Create the Books model
class Books extends Illuminate\Database\Eloquent\Model {
    public $timestamps = false;
}

// Grab a book with an id of 1
$book = Books::find(1);

// Change some stuff
$book->name = "The Best Book in the World";
$book->author = "Ed Zynda";

// Save it to the database
$book->save();
```

Check out the Laravel <a title="Laravel Eloquent documentation" href="http://laravel.com/docs/eloquent" target="_blank">documentation</a> for more information on working with Eloquent.

 [1]: http://www.edzynda.com/media/orm.jpg
