---
title: 'Tip: Custom Scripts In WordPress'
author: Ed Zynda
layout: post
permalink: /tip-custom-scripts-and-stylesheets-in-wordpress/
comments: true
categories:
  - Tips
  - Web Development
  - Wordpress
tags:
  - assets
  - javascript
  - jquery
  - wordpress
thumbnail: /media/wp-code.png
---
WordPress is a great CMS (Content Management System) is you want to get a site up and running very quickly. It&#8217;s themable and within the theming framework, WordPress includes the ability to include popular assets like JQuery. All it takes is a simple command.

```php
<?php
wp_enqueue_script('jquery');
```

What if you don&#8217;t want to use the version of JQuery included in WordPress though? There are several reasons you would require a different version, compatibility for example.

In order to use a custom asset you can do something like the following.  

```php
<?php
wp_deregister_script('jquery');
wp_register_script('jquery', 'http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js', false);
wp_enqueue_script('jquery'); 
```  

This will deregister the current jquery script which is the default included with WordPress and set it to your custom version. You can also register your own scripts like this.  

```php
<?php
wp_register_script('myajaxscript', 'mythemedir/js/ajaxstuff.js', false);
wp_enqueue_script('myajaxscript');
```

 [1]: http://www.edzynda.com/media/wp-code.png
