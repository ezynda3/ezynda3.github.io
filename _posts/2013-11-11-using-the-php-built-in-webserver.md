---
title: Using The PHP Built-in Webserver
author: Ed Zynda
layout: post
permalink: /using-the-php-built-in-webserver/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - PHP
  - Tutorials
  - Web Development
tags:
  - 5.4
  - apache
  - nginx
  - PHP
  - Python
  - Ruby
  - server
---
[<img src="http://www.edzynda.com/media/php-ele-300x195.jpg" alt="PHP Elephant" width="300" height="195" class="alignnone size-medium wp-image-535" />][1]

For years Ruby and Python have been stealing the show with innovative features and functionality to help developers get their projects off the ground. PHP was left in the dust for quite a while. One simple but invaluable feature that PHP had been lacking was it&#8217;s own webserver. Since version 5.4 however, PHP developers can now enjoy this modern convenience.

<!--more-->

[<img src="http://www.edzynda.com/media/php-ele-300x195.jpg" alt="PHP Elephant" width="300" height="195" class="alignnone size-medium wp-image-535" />][1]

For years Ruby and Python have been stealing the show with innovative features and functionality to help developers get their projects off the ground. PHP was left in the dust for quite a while. One simple but invaluable feature that PHP had been lacking was it&#8217;s own webserver. Since version 5.4 however, PHP developers can now enjoy this modern convenience.

Not all backend developers are system administrators. They know how to code and code well. That doesn&#8217;t mean they know how to optimally configure an Apache or Nginx webserver. Depending on the project they work on, their code can live on any number of server configurations. Rather than worry about setting up Apache, developers should be making sure their code works. Leave the configuration to the sys admins.

Using the PHP built-in web server is simple. Simply &#8216;cd&#8217; into the directory you want served and run php with the &#8216;-S&#8217; option.

[sh]  
$ cd ~/public_html  
$ php -S localhost:8000  
[/sh]

Most people will bind it to localhost for development but you can also bind it to any interface like so.

[sh]  
$ php -S 0.0.0.0:8000  
[/sh]

If you need to handle special filetypes you can use a routes file like so.

[php]  
<?php<br /> // router.php

  
$path = pathinfo($\_SERVER["SCRIPT\_FILENAME"]);  
if ($path["extension"] == "el") {  
// Handle .el files  
header("Content-Type: text/x-script.elisp");  
readfile($\_SERVER["SCRIPT\_FILENAME"]);  
}  
else {  
return false; // serve files normally  
}  
[php]

Then run the server.

[sh]  
$ php -S localhost:8000 router.php  
[/sh]

What if you want your scripts to have slightly different behavior when running under the PHP server rather than production? Just use the php\_sapi\_name() function like this.

[php]  
<?php<br /> // router.php

  
if (php\_sapi\_name() == 'cli-server') {  
/\* route static assets and return false \*/  
}  
/\* go on with normal index.php operations \*/  
?>  
[/php]

With this nifty feature it&#8217;s possible to speed up development and cut down dependencies on special development servers or VMs in most cases.

 [1]: http://www.edzynda.com/media/php-ele.jpg