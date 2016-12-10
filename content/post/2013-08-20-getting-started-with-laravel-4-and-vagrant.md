---
author: Ed Zynda
categories:
- Frameworks
- Laravel
- Tutorials
- Web Development
comments: true
date: 2013-08-20T00:00:00Z
share: true
tags:
- framework
- laravel
- PHP
- vagrant
- virtual machine
- virtualbox
title: Getting Started With Laravel 4 and Vagrant
url: /getting-started-with-laravel-4-and-vagrant/
---

I've worked with a handful of different PHP frameworks over the years. From ZEND Framework to Codeigniter, all have their pros and cons. Some of them are more difficult to work with than others. None of them, however, have been more enjoyable for me to work with than Laravel.

I came across Laravel while randomly browsing the web. I don't even remember what I was looking for but after reading more about Laravel and how simple it was to setup, I was hooked. In this article I'll take you through setting up a PHP development environment for Laravel 4.

In this tutorial, I will also be introducing you to a utility called Vagrant. Vagrant makes it ridiculously easy to create a virtual machine in which to run and test your web applications. It uses VirtualBox as a backend and is pretty straightforward once you get into it. No more fussing with WAMP, XAMP, or MAMP environments. You can easily create a small virtual machine that more closely resembles your production environment with a just few commands.

**1. Install VirtualBox and Vagrant**

On a standard Debian or Ubuntu box you should be able to run  

```bash  
$ sudo apt-get install virtualbox  
$ sudo apt-get install vagrant  
$ sudo apt-get install linux-headers-$(uname -r)  
$ sudo dpkg-reconfigure virtualbox-dkms  
```

This installs VirtualBox, Vagrant and it's dependencies. It also compiles the various kernel modules needed for VirtualBox to run correctly.

On my Gentoo box, I run  

```bash  
$ emerge virtualbox  
$ emerge virtualbox-modules  
$ emerge vagrant  
```  

**2. Setup The Vagrant Environment**

Vagrant's virtual machines instances are based on prebuilt configurations called 'boxes'. The following command will download a box based on Ubuntu Precise Pangolin (32bit) and name it 'precise32&#8242;.  

```bash  
$ vagrant box add precise32 http://files.vagrantup.com/precise32.box  
```

You now no longer have to download that box and can create any number of VM instances based on this build.  

```bash
$ mkdir my_dev_env
$ cd my_dev_env
$ vagrant init precise32
$ vagrant up
```

That's it! You now have a fully functioning Ubuntu VM ready to play around with. To work with the VM run  

```bash
$ vagrant ssh  
```

Now you will notice that the VM doesn't automatically have a webserver or mysql or really anything needed for a development environment installed. You can go ahead and install everything you need manually but fortunately, Vagrant is scriptable. You can go ahead and write a script to install everything you need after running 'vagrant up' but why do that when it can be done for you?

Head on over to PuPHPet to see what I mean.

This handy GUI will take you through configuring a Vagrant environment that's functional as soon as it boots up.

First select 'Create Locally'. Next, for this tutorial we'll select Debian Wheezy as our base. There's no reason to change the vagrant box name but you can change the 'Name Your Box' section if you wish. You can also change the ip address that this VM will be accessed from.

One final thing I do in this section is change the default sync folder to './work'. I'll explain why later.

[<img class="alignnone size-full wp-image-259" alt="PuPHPet-step-1-" src="http://www.edzynda.com/media/PuPHPet-step-1-.png" width="680" height="400" />][2]

On the Server Basics section you can safely leave everything as default. If you want to add things like your own personal aliases file, you can add that here.

[<img class="alignnone size-full wp-image-260" alt="PuPHPet-step-2" src="http://www.edzynda.com/media/PuPHPet-step-2.png" width="640" height="400" />][3]

For the HTTP Server I choose Nginx because it's awesome. Choose the host name you want to use for you dev environment. We'll add that to our /etc/hosts file later for easy access. Everything else can safely stay the same.

[<img class="alignnone size-full wp-image-263" alt="PuPHPet-step-3" src="http://www.edzynda.com/media/PuPHPet-step-3.png" width="640" height="400" />][4]

I keep everything here as default as well. Make sure 'Install Composer' is checked. We'll need that to install Laravel later. You can add any other PHP modules you want if you wish.

[<img class="alignnone size-full wp-image-264" alt="PuPHPet-step-4" src="http://www.edzynda.com/media/PuPHPet-step-4.png" width="640" height="400" />][5]

The Database section should be pretty straightforward. Just create a root password and setup a non-root user.[<img class="alignnone size-full wp-image-265" alt="PuPHPet-step-5" src="http://www.edzynda.com/media/PuPHPet-step-5.png" width="640" height="400" />][6]

Finally you can hit 'Create My Manifest'.

[<img class="alignnone size-full wp-image-266" alt="PuPHPet-step-6" src="http://www.edzynda.com/media/PuPHPet-step-6.png" width="640" height="280" />][7]

Once the .zip file has finished downloading run the following  

```bash  
$ unzip wheezy64.zip  
$ mv wheezy64 laravel_dev  
$ cd laravel_dev  
$ ls  
```

You'll notice that this directory is cluttered with stuff. Normally Vagrant uses the top level directory of your environment to sync with a folder on the VM. In this case it will sync with '/var/www'. This comes in handy for developing on the local machine. Remember that we chose './work' as our local sync directory back in the GUI. It's not created by default, so we have to create it ourselves. After that we can start Vagrant as normal.  

```bash  
$ mkdir work  
$ vagrant up  
```  

To test to make sure everything is working run the following  

```bash  
$ sudo echo '192.168.56.101 laravel.dev' >> /etc/hosts  
$ cd work  
$ echo '<?php phpinfo(); ?>' > index.php
```

Note that 'laravel.dev' should be replaced with whatever you named your virtualhost back in the GUI. Open your web browser of choice and go to 'http://laravel.dev' and you should see the familiar PHP Info page.

**3. Install Laravel**

Now to install Laravel we are going to use Composer which is an easy utility for installing various PHP related software such as the Laravel framework.  

```bash  
$ vagrant ssh  
$ cd /var/www  
$ composer create-project laravel/laravel your-project-name --prefer-dist  
$ exit  
$ chmod -R 777 my-project/app/storage  
```

Now point your web browser to 'http://laravel.dev/my-project/public'. You may have to 'chmod -R 777&#8242; the storage directory one more time as Laravel creates a 'session' directory that needs to be writeable.

If all goes well you should see the Laravel welcome screen stating that &#8220;You Have Arrived.&#8221;

[<img class="alignnone size-medium wp-image-270" alt="laravel-success" src="http://www.edzynda.com/media/laravel-success-300x168.jpg" width="300" height="168" />][8]

Congratulations! You should now have a functioning Laravel 4 development environment.

 [1]: http://www.edzynda.com/media/laravel-l-slant.png
 [2]: http://www.edzynda.com/media/PuPHPet-step-1-.png
 [3]: http://www.edzynda.com/media/PuPHPet-step-2.png
 [4]: http://www.edzynda.com/media/PuPHPet-step-3.png
 [5]: http://www.edzynda.com/media/PuPHPet-step-4.png
 [6]: http://www.edzynda.com/media/PuPHPet-step-5.png
 [7]: http://www.edzynda.com/media/PuPHPet-step-6.png
 [8]: http://www.edzynda.com/media/laravel-success.jpg
