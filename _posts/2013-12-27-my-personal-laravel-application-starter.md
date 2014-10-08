---
title: My Personal Laravel Application Starter
author: Ed Zynda
layout: post
permalink: /my-personal-laravel-application-starter/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Uncategorized
---
Frameworks make it easy to hit the ground running when writing web apps. The one hurdle I run into consistently when starting any app though is creating a base user authentication system. It&#8217;s pretty simple to do with most modern frameworks like Laravel but it&#8217;s also just an annoying boilerplate task. This is just one of many boilerplate tasks that I&#8217;ve decided to eliminate by creating a basic starter app that I can pull from GitHub and modify from there.

<!--more-->

Frameworks make it easy to hit the ground running when writing web apps. The one hurdle I run into consistently when starting any app though is creating a base user authentication system. It&#8217;s pretty simple to do with most modern frameworks like Laravel but it&#8217;s also just an annoying boilerplate task. This is just one of many boilerplate tasks that I&#8217;ve decided to eliminate by creating a basic starter app that I can pull from GitHub and modify from there.

To grab a copy of my repo you can simply run

[sh]  
$ git clone https://github.com/ezynda3/app-starter.git  
[/sh]

Right now it only has basic user authentication without any way to add users except manually through mysql or through a seeder file.

If you are familiar with Grunt, I&#8217;ve setup a Gruntfile which will monitor the assets in the &#8216;app/assets&#8217; folder. Simply open an empty terminal an run &#8216;grunt&#8217; inside the top level of your app directory.

I plan to improve upon this with other boilerplate items like simple 404 pages and the like in the future. Feel free to send a pull request if you have something useful to contribute.