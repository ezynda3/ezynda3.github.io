---
author: Ed Zynda
categories:
- PHP
- Continuous Delivery
- Web Development
comments: true
date: 2015-06-03T00:00:00Z
share: true
tags:
- PHP
- Environment
- Feature Toggle
- Continuous Delivery
- Packagist
- Library
title: Simple Environment Variable Based Feature Toggles
url: /simple-environment-variable-based-feature-toggles/
---

There's a lot of talk about continuous delivery in software development these days. One core concept of continuous delivery is "Deploy early and deploy often." Rather than deploy an entire application once it's complete, you should be pushing many small features as they come. There are many benefits to this but it isn't without risk.

Using a software version controll system like git can make working on code in teams easy but can just as easily turn into a nightmare when there are too many active branches from the master branch in the repository. What if you have some code that's been greenlighted for production in your staging branch but some other code that isn't. You can try and merge just the greenlighted code but more often than not you'll have a hard time trying to cherry pick and even if you do, it becomes a mess later when new features are being brought into the mix.

To get around this, you could just merge everything and hope for the best or you could wrap your new code in what's called a feature toggle. What's a feature toggle? Simply put, it's just a way to enable or disable your code from running given a condition. A toggle can be very complex like having your code run only if the user is coming from a block of ip addresses or very simple like running code if an environment varible is set to true. I wrote small library that does the latter.

### Installation

You can install using composer.


```sh
$ composer require ezynda3/feature-toggle
```

### Usage

The library uses the awesome 'vlucas/phpdotenv' library so to create a feature toggle first create a file called '.env' in the root of your project. Then create an environment variable prefixed with 'FEATURE_' and then whatever name you would like to give it.

```sh
FEATURE_SUPER_AWESOME_STUFF=true
```
You don't have to use a '.env' file. You can set environment variables via the command line or in your hosting provider's admin panel assuming they ofter setting environment variables for your deployed applications.

A feature is always set to false or disabled unless you specifically set it to true. Now you can use the feature toggle like so.

```php
<?php
require 'vendor/autoload.php';

use Ezynda3\FeatureToggle\Manager;

$toggle = new Manager(___DIR___);
$feaure = $toggle->feature('super_awesome_stuff');

if ($feature->isEnabled()) {
	// New code
} else {
	// Old code
}
```
That's all there is to it. Be sure to check out the [project](https://github.com/ezynda3/feature-toggle) on GitHub and if there are any features you'd like to add, feel free to submit a pull-request.