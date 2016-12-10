---
author: Ed Zynda
categories:
- PHP
- AOP
- Web Development
comments: true
date: 2015-06-06T00:00:00Z
share: true
tags:
- PHP
- AOP
- Aspect Oriented Programming
- PECL
- Extensions
- Interceptors
title: Aspect Oriented PHP And The Interceptor Pattern
url: /aspect-oriented-php-and-the-interceptor-pattern/
---

There are many ways to modify the behavior of existing code with actually changing the core logic. Some patterns you might be familiar with are the decorator pattern or the observer pattern. Both allow you to take another object and modify the behavior by wrapping your modifcations around the original code. One pattern you might not be familiar with though, is the interceptor pattern.

The interceptor pattern is a core concept of what is called aspect oriented programming (AOP). AOP aims to improve the modularity of your code by allowing you to separate cross-cutting concerns from the rest of your code.

An example of a cross-cutting concern is logging. You may want to log when a particular part of your application completes an action but where should the actual logging code go? This can get hairy if you aren't careful. Something like a logger doesn't really fit well into object oriented programing and may lead to code-duplication or scattering. AOP allows you keep your logging code separate from everything else. You can then attach your logger to other parts of the application by using interceptors.

PHP doesn't come with a built in way for using AOP but luckily there is a very promising PECL extension that provides just that.

### Installation
You can find the project, which is currently in beta, on [GitHub](https://github.com/AOP-PHP/AOP). You should be able to install on your favorite Linux distro by runnning:

```sh
pecl install aop-beta
```
If you had issues like I did, try cloning the repo and following the install instructions in the README.

### Using Interceptors

An interceptor allows you to modify the behavior of a method by literally intercepting the call before, after or both (around). To do this you specify when you want to intercept the method. This is called the **join-point**. Next you specify the method or methods you want to intercept. This is called the **pointcut**. Finally you specify the function you want to use as your interceptor. This is known as an **advice**. The combination of a pointcut and an advice is known as an **aspect**.

The AOP PHP extension provides some handy functions for creating your own interceptors. Let's take a look at a few of them.

####aop\_add\_before
This function will create an interceptor that executes, as the name implies, before the method you want to intercept. Check out the following example.

```php
<?php

class Admin {
    public function showDashboard()
    {
        echo 'Welcome to the admin panel.';
    }
}

function restrictAccessAdvice()
{
    if ($_SESSION['user_type'] !== 'admin') {
        throw new Exception('Access denied!');
    }
}

aop_add_before('Admin->showDashboard()', 'restrictAccessAdvice');

$_SESSION['user_type'] = 'user';
$admin = new Admin();

try {
    $admin->showDashboard();
} catch (Exception $e) {
    echo $e->getMessage();
}
```
In that example we have a really lame class for showing an admin dashboard. We can easily add a layer of security by intercepting method calls related to admin. As you probably can tell, the output from the above code would be this.

```sh
Access denied!
```

You can match a bunch of methods in the same class by doing something like this.

```php
<?php
aop_add_before('Admin->*()', 'restrictAccessAdvice');
```

####aop\_add\_after
This function of course creates an interceptor that executes after the specified method.

```php
<?php

class Service
{
    public function doSomething()
    {
        echo 'Doing something important!' . PHP_EOL;
    }
}

$loggingAdvice = function() {
    echo 'Logging something important!' . PHP_EOL;
};

aop_add_after('Service->doSomething()', $loggingAdvice);

$service = new Service();
$service->doSomething();
```

Here's the output from the that example.

```sh
Doing something important!
Logging something important!
```

####aop\_add\_around
This function combines both before and after but the signature is a little different. The advice you specify should take an argurment. The argument passed in is an object representing the join-point. Check out the example below.

```php
<?php

class MeatFactory
{
    public function produceHam()
    {
        echo "Here is some ham." . PHP_EOL;
    }
}

$sandwichAdvice = function(AopJoinPoint $joinPoint)
{
    // before
    echo "Here is some bread." . PHP_EOL;

    // execute
    $joinPoint->process();

    // after
    echo "Here is some more bread." . PHP_EOL;
};

aop_add_around('MeatFactory->produce*()', $sandwichAdvice);

$factory = new MeatFactory();
$factory->produceHam();
```
Not that we had to explicitly invoke the 'process()' method in order to get any result from the intercepted method. If we wanted to, we could supress the behavior of the intercepted method simply by not calling 'process()'.

Here's the output.

```sh
Here is some bread.
Here is some ham.
Here is some more bread.
```

####Modifying Arguments
By passing the AopJoinPoint object to your advice, you can grab all kinds of information about the method being intercepted. You can even modify the arguments passed to the method.

```php
<?php

class Greeter
{
    public function sayHello($name)
    {
        echo "Hello {$name}!";
    }
}

function ucAdvice(AopJoinPoint $joinPoint)
{
    $args = $joinPoint->getArguments();
    $args[0] = ucwords($args[0]);
    $joinPoint->setArguments($args);
}

aop_add_before('Greeter->sayHello()', 'ucAdvice');

$greeter = new Greeter();
$greeter->sayHello('dave');
```
Intercepting the method before it runs we can use the 'getArguments()' method to grab the original arguments, modify them and then re-pass them for execution using the 'setArguments()' method.

Here is the output.

```sh
Hello Dave!
```

#### Modifying Return Variables
Modifying return variables is somewhat the same as modifying arguments but you can only do this if you are intercepting after or around a method.

```php
<?php

class Calc
{
    public function sum($a, $b)
    {
        return $a + $b;
    }
}

function squareAdvice(AopJoinPoint $joinPoint)
{
    $value = $joinPoint->getReturnedValue();
    $joinPoint->setReturnedValue($value * $value);
}

aop_add_after('Calc->sum()', 'squareAdvice');

$calc = new Calc();
echo $calc->sum(1, 2);
```
Just like when we grabbed the arguments we can grab the original return value using 'getReturnedValue()'. The result of this example would, of course, be 9.

That's just a handful of things you can do with the AOP extension. Checkout the [documentation](http://www.croes.org/gerald/projects/aop/documentation_aop_extension_php.pdf) to learn how to do other things like intercept read/write actions on specific class properties.
