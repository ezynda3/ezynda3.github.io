---
title: Using Hooks In WordPress
author: Ed Zynda
layout: post
permalink: /using-hooks-in-wordpress/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
lazy_seo_meta_key:
  - 
lazy_seo_meta_key_geo:
  - geo1
categories:
  - Tutorials
  - Web Development
  - Wordpress
tags:
  - filters
  - hooks
  - wordpress
---
[<img src="http://www.edzynda.com/media/Fishing_hooks_on_white_surface-300x296.jpg" alt="Hooks" width="300" height="296" class="alignnone size-medium wp-image-512" />][1]

Every so often I have clients who want me to work on WordPress sites or plugins. I honestly hate working with WordPress. While it&#8217;s one of the most popular CMS products in the world with a massive developer base, I find that anything coded for WordPress tends to follow a tacky and poorly organized form of coding. Coding styles vary wildly from developer to developer and there really is no standard. This makes doing modifications a nightmare.

<!--more-->

  
[<img src="http://www.edzynda.com/media/Fishing_hooks_on_white_surface-300x296.jpg" alt="Hooks" width="300" height="296" class="alignnone size-medium wp-image-512" />][1]

Every so often I have clients who want me to work on WordPress sites or plugins. I honestly hate working with WordPress. While it&#8217;s one of the most popular CMS products in the world with a massive developer base, I find that anything coded for WordPress tends to follow a tacky and poorly organized form of coding. Coding styles vary wildly from developer to developer and there really is no standard. This makes doing modifications a nightmare.

As crazy as WordPress development is, it makes up for it with some very helpful features for developers. In the early days of WordPress, if you wanted to add or modify functionality you had to modify the core of WordPress. This is dangerous because as soon as an update comes out, your modification might get wiped out. WordPress then introduced theming and plugin functionality. Along with those features, they allowed for developers to &#8216;hook&#8217; into actual WordPress processes without modifying the core source code.

The main concept that WordPress developers should be familiar to is &#8216;The Loop&#8217;. Basically every page you go to on a WordPress site is the result of &#8216;The Loop&#8217;. It&#8217;s a set of steps that WordPress goes through to build page you are viewing. For example to view a single post, WordPress might pull the post from the database then call a header() function which pulls in the header template then a content() function and a footer() function then finally rendering the page.

Let&#8217;s say that we want to add some extra meta tags to every wordpress page? How would we go about this? First we decide which process we want to hook into. WordPress has quite a few hooks. For this example we&#8217;ll use the &#8216;wp\_head&#8217; hook. To use a hook we use the &#8216;add\_action&#8217; function.

[php]  
add\_action(&#8216;wp\_head&#8217;, &#8216;insert\_meta\_tags&#8217;);

function insert\_meta\_tags() {  
echo &#8216;<meta property="info" content="Some info" />
&#8216;;
  
echo &#8216;<meta property="info2" content="Some more info" />
&#8216;;
  
}  
[/php]

Basically you specify the hook you want to access and then a function to be called. Once WordPress hits the &#8216;wp\_head&#8217; portion of &#8216;The Loop&#8217; it will call the function that you registered. This particular hook is called an action hook. There is another popular hook type called a filter hook. Filter hooks are somewhat different in that they are used to modify specific content like your post title or content. To use a filter hook, we call the &#8216;add\_filter&#8217; function.

[php]  
add\_filter(&#8216;the\_content&#8217;, &#8216;add\_some\_stuff\_to\_content&#8217;);

function add\_some\_stuff\_to\_content($content) {  
$content .= &#8216;Blah blah blah&#8217;;

return $content;  
}  
[/php]

When you create a filter your callback function is passed the variable that you&#8217;re modifying which is in this case, the content of a post. In this case we appended &#8216;Blah blah blah&#8217; to it and then returned it.

This is all really just basic stuff but hopefully it gives you an idea of how you can make useful modifications to WordPress.

 [1]: http://www.edzynda.com/media/Fishing_hooks_on_white_surface.jpg