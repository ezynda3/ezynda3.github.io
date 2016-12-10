---
author: Ed Zynda
categories:
- Web Development
comments: true
date: 2013-08-16T00:00:00Z
share: true
tags:
- apache
- linode
- nginx
- vps
- web server
title: 3 Reasons To Use Nginx Instead Of Apache
url: /3-reasons-to-use-nginx-instead-of-apache/
---

If you&#8217;ve been working with the web for the past decade, you&#8217;re probably familiar with Apache web server. Apache has long been a pillar of the world wide web. It&#8217;s free, functional and very modular. I&#8217;ve been using Apache for years as it&#8217;s the default on many hosting solutions.

As great as Apache is, it&#8217;s not a one size fits all solution. In fact it may even be overkill for some projects. About six months ago, I traded in my old hosting provider for a Virtual Private Server (VPS) at<a title="Xen VPS Hosting" href="http://www.linode.com/?r=e023858b4fde04bffe508bb43c30bad4ee47a9f0" target="_blank"> Linode</a>. Around the same time, I started reading about a new web server called <a title="Nginx" href="http://nginx.org/" target="_blank">Nginx</a>. While not the most popular sever, it was gaining in popularity. At first I wasn&#8217;t sure about switching but after some research, I decided to take the plunge. Here are 3 reasons you should consider trying out Nginx for yourself.

**1. It&#8217;s Lightweight**

If you&#8217;ve ever worked with Apache&#8217;s configuration files you&#8217;re familiar with how long and cluttered they are. Apache has a lot of features and options. You really don&#8217;t need all of those features for most projects though.

Nginx is very simple in that it just serves static web pages while adding support for essential things like virtual hosts and URL rewrites. There are no modules for PHP or JSP. Instead those requests are passed efficiently to third party processors. This leaves Nginx&#8217;s memory footprint small.

**2. It Serves Static Files FAST**

Like I mentioned before, Nginx really only serves static web pages. Being less memory and CPU hungry than Apache, it serves them up much faster.

**3. Proxying Makes It Scalable**

Most projects aren&#8217;t full of static content but rather dynamic. If you want to incorporate Python or Ruby on Rails into your project, it&#8217;s as simple as adding a proxy directive to your configuration.

Start a Python or Rails server on a particular port like 8000 and add something like this.  

```nginx  
location /railsapp {  
    proxy_pass http://127.0.0.1:8000;  
    proxy_redirect off;  
}  
```

The Python or Rails server can do all the heavy lifting when it needs to and Nginx can do what it does best.

Apache is great for a lot of things and I&#8217;m not saying it&#8217;s no longer needed but if your project doesn&#8217;t require everything under the sun, you might want to consider giving Nginx a try.

 [1]: http://www.edzynda.com/media/apache_nginx_benchmark.png
