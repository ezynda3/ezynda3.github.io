---
author: Ed Zynda
categories:
- Frameworks
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-13T00:00:00Z
share: true
tags:
- Composer
- framework
- microframework
- PHP
- silex
- symfony
title: What Is A Microframework? Getting Started With Silex
url: /what-is-a-microframework-getting-started-with-silex/
---

There is no shortage of coding frameworks these days. I can't scroll through my twitter and facebook feeds or my favorite dev forums without stumbling upon something new. Like a hungry barracuda, I'm instantly attracted to new shiny things. While browsing reddit.com/r/php I came across a mention of a microframework called <a href="http://silex.sensiolabs.org/" title="Silex" target="_blank">Silex</a>. Now you may be wondering what a microframework is. I was wondering the same thing, so I took a look at the project's website to figure out for myself.

It turns out that a microframework, just like the name implies, is just a barebones framework meant to give the developer access to powerful tools without the bulk of a full-on framework. Silex is a microframework which utilizes components from the popular <a href="http://symfony.com/" title="Symfony2" target="_blank">Symfony2</a> framework. In fact it's written and maintained by the same people at Sensiolabs. It's meant for applications consisting of usually one php file but you can definitely build something much bigger. Installing and using Silex is pretty straight-forward especially if you are familiar with other modern PHP frameworks.

Like all modern PHP projects you start with a composer.json file.

```json 
{
    "require": {
        "silex/silex": "~1.1"
    }
}
```

Run composer.

```bash  
$ composer install  
```

Now create a public facing directory with an index.php file.  

```bash  
$ mkdir public  
$ touch index.php  
```

Now you should end up with the following directory structure.

```bash  
├── composer.json  
├── composer.lock  
├── vendor  
│ └── ...  
└── public  
└── index.php  
```

Now we need to bootstrap Silex so we can start using it in our index.php file.

```php  
<?php
// public/index.php
 
require_once __DIR__.'/../vendor/autoload.php';
 
$app = new Silex\Application;
 
// definitions
 
$app->run();
```

In the above snippet, we've created an instance of Silex called '$app'. Now we can use '$app' to define things like routes and service providers before calling '$app->run()' which will load our application.

Defining routes is pretty simple. For each route you need to know the method (e.g. GET or POST), the URI pattern you want to match and method.

```php  
<?php
// public/index.php
 
require_once __DIR__.'/../vendor/autoload.php';
 
use Symfony\Component\HttpFoundation\Request;
 
$app = new Silex\Application;
 
// definitions
$app->get('/', function()
{
    return '<form method="post" action="/">Enter your name: <input type="text" name="name" /><input type="submit" value="Submit" /></form>';
});
 
$app->post('/', function(Request $request)
{
    $name = $request->get('name');
    return "Hello! Welcome " . $name . "!";
});
 
$app->run();
```

In the above example, we defined two routes. The GET request returns a form which is then handled by our POST route once it is submitted. To access the variables passed in our POST request we use Symfony's HttpFoundation\Request component and the 'get()' method. An instance of of Request is automatically injected into our route closure by Silex using type-hinting or what I like to call magic.

Hopefully from this tutorial you can get an idea for how to create a small yet powerful app using Silex.