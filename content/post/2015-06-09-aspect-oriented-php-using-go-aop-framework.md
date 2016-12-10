---
author: Ed Zynda
categories:
- PHP
- AOP
- Web Development
comments: true
date: 2015-06-09T00:00:00Z
share: true
tags:
- PHP
- AOP
- Aspect Oriented Programming
- Framework
- Interceptors
title: Aspect Oriented PHP Using Go! AOP Framework
url: /aspect-oriented-php-using-go-aop-framework/
---

In my [last article](/aspect-oriented-php-and-the-interceptor-pattern)  we took a look at using aspect oriented programming (AOP) in PHP by installing the excellent AOP PECL extension for PHP. This is great if you have admin access to the servers you run your code on. It's very common though, for people to run their code on shared hosting. These types of providers don't usually provide a way for you to install any extra PHP extensions besides the common extensions they provide. It would much easier if there was a package we could use instead. Lucky for us, there is!

The [Go! AOP Framework](https://github.com/goaop/framework) for PHP was created by [Alexander Lisachenko](https://github.com/lisachenko) and provides a way to use AOP in PHP without installing an extension. It accomplishes this by cleverly using autoloading, reflection and some other nifty features of modern PHP.

###Installation

First we need to install the package with composer.

Let's created a simple compose.json file with our dependencies and also define a simple namespace for our code.

```json
{
    "require": {
        "goaop/framework": "^0.6.0"
    },
    "autoload": {
        "psr-4": {
            "Stark\\": "src/"
        }
    }
}
```
Now we can install from the command line.

```sh
$ composer install
```
Next we need to create what we'll call our AOP kernel. This is where we will register our **aspects**, as we'll see later.

```php
// src/AOP/Kernel.php
<?php namespace Stark\AOP;

use Go\Core\AspectKernel;
use Go\Core\AspectContainer;

class Kernel extends AspectKernel
{
    /**
     * Configure an AspectContainer with advisors, aspects and pointcuts
     *
     * @param AspectContainer $container
     *
     * @return void
     */
    protected function configureAop(AspectContainer $container)
    {
    }
}
```
Third we need to initialize the kernel. This should ideally be done in your application's front controller.

```php
// public/index.php
<?php
include __DIR__ . '/../vendor/autoload.php'; // use composer's autoloader

// Initialize an application aspect container
$applicationAspectKernel = \Stark\AOP\Kernel::getInstance();
$applicationAspectKernel->init([
    'debug' => true, // use 'false' for production mode
    // Cache directory
    'cacheDir' => __DIR__ . '/../cache'
]);
```
Finally we can start creating aspects to modify our code.

### Using Go! AOP

Let's pretend we have a little Facebook service.

```php
// src/Service/Facebook.php
<?php namespace Stark\Service;

class Facebook
{
    public function doStuff()
    {
        echo "Facebook stuff!";
    }
}
```
Now let's create an aspect to intercept any method called from that class.

```php
// src/AOP/Aspect/Monitor.php
<?php namespace Stark\AOP\Aspect;

use Go\Aop\Aspect;
use Go\Aop\Intercept\FieldAccess;
use Go\Aop\Intercept\MethodInvocation;
use Go\Lang\Annotation\After;
use Go\Lang\Annotation\Before;
use Go\Lang\Annotation\Around;
use Go\Lang\Annotation\Pointcut;

/**
 * Monitor aspect
 */
class Monitor implements Aspect
{

    /**
     * Method that will be called before real method
     *
     * @param MethodInvocation $invocation Invocation
     * @Before("execution(public Stark\Service\Facebook->*(*))")
     */
    public function beforeMethodExecution(MethodInvocation $invocation)
    {
        $obj = $invocation->getThis();
        echo 'Calling Before Interceptor for method: ',
        is_object($obj) ? get_class($obj) : $obj,
        $invocation->getMethod()->isStatic() ? '::' : '->',
        $invocation->getMethod()->getName(),
        '()',
        "<br>\n";
    }
}
```
Next we have to register the new aspect in our kernel.

```php
// src/AOP/Kernel.php
<?php namespace Stark\AOP;

use Go\Core\AspectKernel;
use Go\Core\AspectContainer;
use Stark\AOP\Aspect\Monitor;

class Kernel extends AspectKernel
{
    /**
     * Configure an AspectContainer with advisors, aspects and pointcuts
     *
     * @param AspectContainer $container
     *
     * @return void
     */
    protected function configureAop(AspectContainer $container)
    {
        $container->registerAspect(new Monitor());
    }
}

```

Finally in our front controller we can add these few lines to run our little service.

```php
// public/index.php
<?php
$fb = new \Stark\Service\Facebook();
$fb->doStuff();
```
If you run index.php you'll get the following output.

```
Calling Before Interceptor for method: Stark\Service\Facebook->doStuff()
Facebook stuff!
```
The Go! AOP Framework uses annotations rather than global functions to define **jointpoints** and **pointcuts**. 'beforeMethodExecution()' is our **advice** in this instance. We used the '@Before' annotation to intercept before the method is called. Similarly we can use '@After' and '@Around' as joinpoints as well.

The rest of the annotation defines the pointcut which is 'execution' in this case. We can also use 'access' for reading/writing of class properties, initialization for when a class is first created and many others.

One other neat trick we can use is intercepting custom annotations. Go! AOP includes Doctrine's annotation reader package so we can create a custom annotation like so.

```php
// src/Annotation/Custom.php
<?php namespace Stark\Annotation;

use Doctrine\Common\Annotations\Annotation;

/**
 * @Annotation
 * @Target("METHOD")
 */
class Custom extends Annotation
{
}
```
Next let's modify our Facebook service...

```php
// src/Service/Facebook.php
<?php namespace Stark\Service;

use Stark\Annotation\Custom;

class Facebook
{
    /**
     * @Custom
     */
    public function doStuff()
    {
        echo "Facebook stuff!";
    }
}
```

...and our aspect.

```php
// src/AOP/Aspect/Monitor.php
<?php namespace Stark\AOP\Aspect;

use Go\Aop\Aspect;
use Go\Aop\Intercept\FieldAccess;
use Go\Aop\Intercept\MethodInvocation;
use Go\Lang\Annotation\After;
use Go\Lang\Annotation\Before;
use Go\Lang\Annotation\Around;
use Go\Lang\Annotation\Pointcut;

/**
 * Monitor aspect
 */
class Monitor implements Aspect
{

    /**
     * Method that will be called before real method
     *
     * @param MethodInvocation $invocation Invocation
     * @Before("@annotation(Stark\Annotation\Custom)")
     */
    public function beforeMethodExecution(MethodInvocation $invocation)
    {
        $obj = $invocation->getThis();
        echo 'Calling Before Interceptor for method: ',
        is_object($obj) ? get_class($obj) : $obj,
        $invocation->getMethod()->isStatic() ? '::' : '->',
        $invocation->getMethod()->getName(),
        '()',
        "<br>\n";
    }
}
```
We should get the same result as before.

This framework is full of features and it opens up a lot of new possibilities in PHP. To learn more about what you can do with Go! AOP, check out the official [documentation](http://go.aopphp.com/docs/).
