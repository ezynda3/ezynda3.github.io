---
author: Ed Zynda
categories:
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-11T00:00:00Z
share: true
tags:
- 5.4
- apache
- nginx
- PHP
- Python
- Ruby
- server
title: Using The PHP Built-in Webserver
url: /using-the-php-built-in-webserver/
---

For years Ruby and Python have been stealing the show with innovative features and functionality to help developers get their projects off the ground. PHP was left in the dust for quite a while. One simple but invaluable feature that PHP had been lacking was it's own webserver. Since version 5.4 however, PHP developers can now enjoy this modern convenience.

Not all backend developers are system administrators. They know how to code and code well. That doesn't mean they know how to optimally configure an Apache or Nginx webserver. Depending on the project they work on, their code can live on any number of server configurations. Rather than worry about setting up Apache, developers should be making sure their code works. Leave the configuration to the sys admins.

Using the PHP built-in web server is simple. Simply 'cd' into the directory you want served and run php with the '-S' option.

```bash  
$ cd ~/public_html  
$ php -S localhost:8000  
```

Most people will bind it to localhost for development but you can also bind it to any interface like so.

```  
$ php -S 0.0.0.0:8000  
```

If you need to handle special filetypes you can use a routes file like so.

```php  
<?php
// router.php
$path = pathinfo($_SERVER["SCRIPT_FILENAME"]);
if ($path["extension"] == "el") {
    // Handle .el files
    header("Content-Type: text/x-script.elisp");
    readfile($_SERVER["SCRIPT_FILENAME"]);
}
else {
    return false; // serve files normally
}
```

Then run the server.

```bash  
$ php -S localhost:8000 router.php  
```

What if you want your scripts to have slightly different behavior when running under the PHP server rather than production? Just use the php\_sapi\_name() function like this.

```php  
<?php
// router.php
if (php_sapi_name() == 'cli-server') {
    /* route static assets and return false */
}
/* go on with normal index.php operations */
?>
```

With this nifty feature it's possible to speed up development and cut down dependencies on special development servers or VMs in most cases.

 [1]: http://www.edzynda.com/media/php-ele.jpg
