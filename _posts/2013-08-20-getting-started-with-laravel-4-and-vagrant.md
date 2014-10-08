---
title: Getting Started With Laravel 4 and Vagrant
author: Ed Zynda
layout: post
permalink: /getting-started-with-laravel-4-and-vagrant/
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dm3sb_custom_sidebar:
  - 
categories:
  - Frameworks
  - Laravel
  - Tutorials
  - Web Development
tags:
  - framework
  - laravel
  - PHP
  - vagrant
  - virtual machine
  - virtualbox
---
[<img class="alignnone size-medium wp-image-280" alt="laravel-l-slant" src="http://www.edzynda.com/media/laravel-l-slant-300x205.png" width="300" height="205" />][1]

I&#8217;ve worked with a handful of different PHP frameworks over the years. From ZEND Framework to Codeigniter, all have their pros and cons. Some of them are more difficult to work with than others. None of them, however, have been more enjoyable for me to work with than Laravel.

<!--more-->

[<img class="alignnone size-medium wp-image-280" alt="laravel-l-slant" src="http://www.edzynda.com/media/laravel-l-slant-300x205.png" width="300" height="205" />][1]

I&#8217;ve worked with a handful of different PHP frameworks over the years. From ZEND Framework to Codeigniter, all have their pros and cons. Some of them are more difficult to work with than others. None of them, however, have been more enjoyable for me to work with than Laravel.

I came across Laravel while randomly browsing the web. I don&#8217;t even remember what I was looking for but after reading more about Laravel and how simple it was to setup, I was hooked. In this article I&#8217;ll take you through setting up a PHP development environment for Laravel 4.

In this tutorial, I will also be introducing you to a utility called Vagrant. Vagrant makes it ridiculously easy to create a virtual machine in which to run and test your web applications. It uses VirtualBox as a backend and is pretty straightforward once you get into it. No more fussing with WAMP, XAMP, or MAMP environments. You can easily create a small virtual machine that more closely resembles your production environment with a just few commands.

**1. Install VirtualBox and Vagrant**

On a standard Debian or Ubuntu box you should be able to run  
[sh]  
$ sudo apt-get install virtualbox  
$ sudo apt-get install vagrant  
$ sudo apt-get install linux-headers-$(uname -r)  
$ sudo dpkg-reconfigure virtualbox-dkms  
[/sh]  
This installs VirtualBox, Vagrant and it&#8217;s dependencies. It also compiles the various kernel modules needed for VirtualBox to run correctly.

On my Gentoo box, I run  
[sh]  
$ emerge virtualbox  
$ emerge virtualbox-modules  
$ emerge vagrant  
[/sh]  
**2. Setup The Vagrant Environment**

Vagrant&#8217;s virtual machines instances are based on prebuilt configurations called &#8216;boxes&#8217;. The following command will download a box based on Ubuntu Precise Pangolin (32bit) and name it &#8216;precise32&#8242;.  
[sh]  
$ vagrant box add precise32 http://files.vagrantup.com/precise32.box  
[/sh]  
You now no longer have to download that box and can create any number of VM instances based on this build.  
[sh]  
$ mkdir my\_dev\_env  
$ cd my\_dev\_env  
$ vagrant init precise32  
$ vagrant up  
[/sh]  
That&#8217;s it! You now have a fully functioning Ubuntu VM ready to play around with. To work with the VM run  
[sh]  
$ vagrant ssh  
[/sh]  
Now you will notice that the VM doesn&#8217;t automatically have a webserver or mysql or really anything needed for a development environment installed. You can go ahead and install everything you need manually but fortunately, Vagrant is scriptable. You can go ahead and write a script to install everything you need after running &#8216;vagrant up&#8217; but why do that when it can be done for you?

Head on over to PuPHPet to see what I mean.

This handy GUI will take you through configuring a Vagrant environment that&#8217;s functional as soon as it boots up.

First select &#8216;Create Locally&#8217;. Next, for this tutorial we&#8217;ll select Debian Wheezy as our base. There&#8217;s no reason to change the vagrant box name but you can change the &#8216;Name Your Box&#8217; section if you wish. You can also change the ip address that this VM will be accessed from.

One final thing I do in this section is change the default sync folder to &#8216;./work&#8217;. I&#8217;ll explain why later.

[<img class="alignnone size-full wp-image-259" alt="PuPHPet-step-1-" src="http://www.edzynda.com/media/PuPHPet-step-1-.png" width="680" height="400" />][2]

&nbsp;

On the Server Basics section you can safely leave everything as default. If you want to add things like your own personal aliases file, you can add that here.

[<img class="alignnone size-full wp-image-260" alt="PuPHPet-step-2" src="http://www.edzynda.com/media/PuPHPet-step-2.png" width="640" height="400" />][3]

&nbsp;

For the HTTP Server I choose Nginx because it&#8217;s awesome. Choose the host name you want to use for you dev environment. We&#8217;ll add that to our /etc/hosts file later for easy access. Everything else can safely stay the same.

[<img class="alignnone size-full wp-image-263" alt="PuPHPet-step-3" src="http://www.edzynda.com/media/PuPHPet-step-3.png" width="640" height="400" />][4]

I keep everything here as default as well. Make sure &#8216;Install Composer&#8217; is checked. We&#8217;ll need that to install Laravel later. You can add any other PHP modules you want if you wish.

[<img class="alignnone size-full wp-image-264" alt="PuPHPet-step-4" src="http://www.edzynda.com/media/PuPHPet-step-4.png" width="640" height="400" />][5]

&nbsp;

The Database section should be pretty straightforward. Just create a root password and setup a non-root user.[<img class="alignnone size-full wp-image-265" alt="PuPHPet-step-5" src="http://www.edzynda.com/media/PuPHPet-step-5.png" width="640" height="400" />][6]

&nbsp;

Finally you can hit &#8216;Create My Manifest&#8217;.

[<img class="alignnone size-full wp-image-266" alt="PuPHPet-step-6" src="http://www.edzynda.com/media/PuPHPet-step-6.png" width="640" height="280" />][7]

&nbsp;

Once the .zip file has finished downloading run the following  
[sh]  
$ unzip wheezy64.zip  
$ mv wheezy64 laravel_dev  
$ cd laravel_dev  
$ ls  
[/sh]  
You&#8217;ll notice that this directory is cluttered with stuff. Normally Vagrant uses the top level directory of your environment to sync with a folder on the VM. In this case it will sync with &#8216;/var/www&#8217;. This comes in handy for developing on the local machine. Remember that we chose &#8216;./work&#8217; as our local sync directory back in the GUI. It&#8217;s not created by default, so we have to create it ourselves. After that we can start Vagrant as normal.  
[sh]  
$ mkdir work  
$ vagrant up  
[/sh]  
To test to make sure everything is working run the following  
[sh]  
$ sudo echo &#8217;192.168.56.101 laravel.dev&#8217; >> /etc/hosts  
$ cd work  
$ echo &#8216;<?php phpinfo(); ?>&#8216; > index.php

  
[/sh]  
Note that &#8216;laravel.dev&#8217; should be replaced with whatever you named your virtualhost back in the GUI. Open your web browser of choice and go to &#8216;http://laravel.dev&#8217; and you should see the familiar PHP Info page.

**3. Install Laravel**

Now to install Laravel we are going to use Composer which is an easy utility for installing various PHP related software such as the Laravel framework.  
[sh]  
$ vagrant ssh  
$ cd /var/www  
$ composer create-project laravel/laravel your-project-name &#8211;prefer-dist  
$ exit  
$ chmod -R 777 my-project/app/storage  
[/sh]  
Now point your web browser to &#8216;http://laravel.dev/my-project/public&#8217;. You may have to &#8216;chmod -R 777&#8242; the storage directory one more time as Laravel creates a &#8216;session&#8217; directory that needs to be writeable.

If all goes well you should see the Laravel welcome screen stating that &#8220;You Have Arrived.&#8221;

[<img class="alignnone size-medium wp-image-270" alt="laravel-success" src="http://www.edzynda.com/media/laravel-success-300x168.jpg" width="300" height="168" />][8]

&nbsp;

Congratulations! You should now have a functioning Laravel 4 development environment.

 [1]: http://www.edzynda.com/media/laravel-l-slant.png
 [2]: http://www.edzynda.com/media/PuPHPet-step-1-.png
 [3]: http://www.edzynda.com/media/PuPHPet-step-2.png
 [4]: http://www.edzynda.com/media/PuPHPet-step-3.png
 [5]: http://www.edzynda.com/media/PuPHPet-step-4.png
 [6]: http://www.edzynda.com/media/PuPHPet-step-5.png
 [7]: http://www.edzynda.com/media/PuPHPet-step-6.png
 [8]: http://www.edzynda.com/media/laravel-success.jpg