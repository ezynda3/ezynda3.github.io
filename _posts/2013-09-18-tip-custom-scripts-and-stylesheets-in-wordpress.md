---
title: 'Tip: Custom Scripts In WordPress'
author: Ed Zynda
layout: post
permalink: /tip-custom-scripts-and-stylesheets-in-wordpress/
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dm3sb_custom_sidebar:
  - 
categories:
  - Tips
  - Web Development
  - Wordpress
tags:
  - assets
  - javascript
  - jquery
  - wordpress
---
[<img class="alignnone size-full wp-image-297" alt="Wordpress Code" src="http://www.edzynda.com/media/wp-code.png" width="200" height="200" />][1]

WordPress is a great CMS (Content Management System) is you want to get a site up and running very quickly. It&#8217;s themable and within the theming framework, WordPress includes the ability to include popular assets like JQuery. All it takes is a simple command.

<!--more-->

[<img class="alignnone size-full wp-image-297" alt="Wordpress Code" src="http://www.edzynda.com/media/wp-code.png" width="200" height="200" />][1]

WordPress is a great CMS (Content Management System) is you want to get a site up and running very quickly. It&#8217;s themable and within the theming framework, WordPress includes the ability to include popular assets like JQuery. All it takes is a simple command.

<pre lang="php">wp_enqueue_script('jquery);</pre>

What if you don&#8217;t want to use the version of JQuery included in WordPress though? There are several reasons you would require a different version, compatibility for example.

In order to use a custom asset you can do something like the following.  
[php]  
wp\_deregister\_script(&#8216;jquery&#8217;);  
wp\_register\_script(&#8216;jquery&#8217;, &#8216;http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js&#8217;, false);  
wp\_enqueue\_script(&#8216;jquery&#8217;);  
[/php]  
This will deregister the current jquery script which is the default included with WordPress and set it to your custom version. You can also register your own scripts like this.  
[php]  
wp\_register\_script(&#8216;myajaxscript&#8217;, &#8216;mythemedir/js/ajaxstuff.js&#8217;, false);  
wp\_enqueue\_script(&#8216;myajaxscript&#8217;);  
[/php]

 [1]: http://www.edzynda.com/media/wp-code.png