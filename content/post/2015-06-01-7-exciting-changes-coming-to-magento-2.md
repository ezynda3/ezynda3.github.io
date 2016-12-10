---
author: Ed Zynda
categories:
- PHP
- Magento
- Web Development
comments: true
date: 2015-06-01T00:00:00Z
share: true
tags:
- PHP
- Magento
title: 7 Exciting Changes Coming to Magento 2
url: /7-exciting-changes-coming-to-magento-2/
---

E-commerce is not my favorite thing to write code for and as such, I'm no expert. I was actually thrown into the world of e-commerce customizations when I started working for my current employer a little over a year ago. The very first project I was assigned to start coding for was built using Magento. While I had heard of it, I knew little to nothing about it except that it wasn't called "Magneto" which is what I originally thought when I first glanced at the name.

Because I was brought on at pretty much the beginning of the project I was able to learn a lot while building the site. The learning curve was steep at first but eventually I started to become familiar with the "Magento way" of doing things. To tell the truth I didn't like it much.

I bursted back into the world of PHP development after a nine year stint in the US Air Force doing something totally un-related. Because I was basically a newb all over again, I dove head first into all the latest literature and video tutorials dealing with how to code "[PHP the right way.](http://www.phptherightway.com/)" I had become familiar with concepts like dependency injection, coding to interfaces, unit testing and a plethora of useful design patterns. Imagine my discomfort when I started writing custom code for Magento.

The autoloader, while very sofisticated, is terribly outdated compared to the PSR-0 and PSR-4 standards. Composer isn't supported by default. There's no DI container to speak of and unit tests are almost impossible to create without needing to bootstrap a database which technically makes them integration tests.

I've since made my peace with Magento and it's silly conventions. I've also come up with some nifty hacks to allow for modern coding practices. Those days are soon to be over though with the coming of Magento 2!

Magento 2 has a handful of welcome changes in store. Here are just a few of them.

### 1. Modules are consolidated into a single directory
I remember getting frustrated with the directory structure of Magento and more specifically the way a module's code could be spread out across multiple directories. Your PHP code lived in 'app/code' while templates and layouts in 'app/design' but several directories deep.

No longer! Now everything that your module requires lives in one directory. This includes PHP code, design templates and config files.

### 2. Config files have been broken down into smaller files
You can do a lot with a single 'config.xml' file in a Magento 1 module. Those config files can get huge and because of that, it's sometimes very easy to get lost in them. To make matters worse, you can have a file filled with improperly formatted config data and Magento 1 won't even complain. This makes it hard to debug.

Magento 2 now breaks that single 'config.xml' file into many smaller files. This makes it easier to see logically where certain things should go. To prevent silent errors, Magento 2 uses XML schema files to validate config files. Now you'll know pretty quickly if you've messed somthing up.

### 3. Dependency injection
Magento 2 supports dependency injection and provides it's own dependency injection container. The container is configured via and XML config file which wouldn't be my first choice but hey I'll take what I can get.

### 4. Interception
In Magento 1, one way to extend the behavior of a core module is to "rewrite" one of the core classes, extend it and rewrite one of it's methods with your custom code. This works great most of the time. The problem arises when there are two custom modules trying to extend the same core class. This is where collisions occur and one module could end up cancelling out the other or worse.

Magento 2 provides what it calls Interception. To customize a core module's method you can simply create what is called a plugin class. In the plugin class you can modify any public methods by creating methods prefixed with 'before', 'after' and 'around' and the tacking on the name of the method you want to change. Then you pass an instance of the core class you are extending. If you are extending something another module is extending, the result is simply passed from one plugin to another. You can specify a sort order as well for more desireable results.

### 5. No more god-class
Yeah I'm not going to miss having to call 'Mage::<whatever>' anymore.

### 6. Semantic versioning
Magento 2 will use semantic versioning for the core product. No more keeping track of different patches to be applied. Simply take a look at the version you are running and check against the latest "patch" version to see if you're running the latest, most secure.

### 7. Tests, tests, tests!
Magento 2 is very test friendly and even includes an extensive set of tests for the core code. This includes unit, integration and even javascript tests. Tests for your modules live inside the module's directory along with everything else.

These just a few of the exciting changes coming to Magento 2. To find out more, checkout of the [GitHub repo](https://github.com/magento/magento2).
