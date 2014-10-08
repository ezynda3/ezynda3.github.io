---
title: What Is A Microframework? Getting Started With Silex
author: Ed Zynda
layout: post
permalink: /what-is-a-microframework-getting-started-with-silex/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Frameworks
  - PHP
  - Tutorials
  - Web Development
tags:
  - Composer
  - framework
  - microframework
  - PHP
  - silex
  - symfony
---
There is no shortage of coding frameworks these days. I can&#8217;t scroll through my twitter and facebook feeds or my favorite dev forums without stumbling upon something new. Like a hungry barracuda, I&#8217;m instantly attracted to new shiny things. While browsing reddit.com/r/php I came across a mention of a microframework called <a href="http://silex.sensiolabs.org/" title="Silex" target="_blank">Silex</a>. Now you may be wondering what a microframework is. I was wondering the same thing, so I took a look at the project&#8217;s website to figure out for myself.

<!--more-->

There is no shortage of coding frameworks these days. I can&#8217;t scroll through my twitter and facebook feeds or my favorite dev forums without stumbling upon something new. Like a hungry barracuda, I&#8217;m instantly attracted to new shiny things. While browsing reddit.com/r/php I came across a mention of a microframework called <a href="http://silex.sensiolabs.org/" title="Silex" target="_blank">Silex</a>. Now you may be wondering what a microframework is. I was wondering the same thing, so I took a look at the project&#8217;s website to figure out for myself.

It turns out that a microframework, just like the name implies, is just a barebones framework meant to give the developer access to powerful tools without the bulk of a full-on framework. Silex is a microframework which utilizes components from the popular <a href="http://symfony.com/" title="Symfony2" target="_blank">Symfony2</a> framework. In fact it&#8217;s written and maintained by the same people at Sensiolabs. It&#8217;s meant for applications consisting of usually one php file but you can definitely build something much bigger. Installing and using Silex is pretty straight-forward especially if you are familiar with other modern PHP frameworks.

Like all modern PHP projects you start with a composer.json file.

[js]  
{  
&#8220;require&#8221;: {  
&#8220;silex/silex&#8221;: &#8220;~1.1&#8243;  
}  
}  
[/js]

Run composer.

[sh]  
$ composer install  
[/sh]

Now create a public facing directory with an index.php file.  
[sh]  
$ mkdir public  
$ touch index.php  
[/sh]

Now you should end up with the following directory structure.

[sh]  
├── composer.json  
├── composer.lock  
├── vendor  
│ └── &#8230;  
└── public  
└── index.php  
[/sh]

Now we need to bootstrap Silex so we can start using it in our index.php file.

[php]  
<?php<br /> // public/index.php

require\_once \\_\_DIR\_\_.'/../vendor/autoload.php';

$app = new Silex\Application;

// definitions

$app->run();  
[/php]

In the above snippet, we&#8217;ve created an instance of Silex called &#8216;$app&#8217;. Now we can use &#8216;$app&#8217; to define things like routes and service providers before calling &#8216;$app->run()&#8217; which will load our application.

Defining routes is pretty simple. For each route you need to know the method (e.g. GET or POST), the URI pattern you want to match and method.

[php]  
<?php<br /> // public/index.php

require\_once \\_\_DIR\_\_.'/../vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;

$app = new Silex\Application;

// definitions  
$app->get(&#8216;/&#8217;, function()  
{  
return &#8216;  
&#8216;;  
});

$app->post(&#8216;/&#8217;, function(Request $request)  
{  
$name = $request->get(&#8216;name&#8217;);  
return &#8220;Hello! Welcome &#8221; . $name . &#8220;!&#8221;;  
});

$app->run();  
[/php]

In the above example, we defined two routes. The GET request returns a form which is then handled by our POST route once it is submitted. To access the variables passed in our POST request we use Symfony&#8217;s HttpFoundation\Request component and the &#8216;get()&#8217; method. An instance of of Request is automatically injected into our route closure by Silex using type-hinting or what I like to call magic.

Hopefully from this tutorial you can get an idea for how to create a small yet powerful app using Silex.