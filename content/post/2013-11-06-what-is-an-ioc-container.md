---
author: Ed Zynda
categories:
- Laravel
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-06T00:00:00Z
share: true
tags:
- bind
- dependency injection
- IoC
- laravel
- OOP PHP
title: What Is An IoC Container?
url: /what-is-an-ioc-container/
---

If you&#8217;re discussing object oriented programming in PHP these days, it&#8217;s likely that you&#8217;ve at least heard about such topics as dependency injection or IoC containers. They&#8217;re both pretty useful tools for coding and testing your code. What exactly is an IoC container though? It sounds pretty complicated but it&#8217;s really not.

If you read my earlier post on using repositories in Laravel you might have noticed that when using dependency injection in our __construct method, we didn&#8217;t have to actually instantiate our repository. Laravel&#8217;s IoC container did it for us. Behind the scenes, when our controller was called, Laravel ran the App::make() method and passed it through to our constructor.

```php
<?php

use Stark\Repositories\UserRespositoryInterface as Repository;

class UserController extends BaseController {
    protected $user;

    public function __construct(Repository $user)
    {
        $this->user = $user;
    }
    public function index()
    {
        $users $this->user->getAll();
        return View::make('users.list', compact($users));
    }
}
```

Now basic object oriented programming says we can&#8217;t instantiate an interface so we have to pass an object that implements the UserRepositoryInterface such as our EloquentUserRepository.

```php
<?php
App::bind('Stark\Repositories\UserRepositoryInterface', 'Stark\Repositories\EloquentUserRepository');
```

Remember that line of code? What we are doing is telling Laravel&#8217;s IoC to respond to any request for UserRepositoryInterface with EloquentUserRepository.

```php
<?php
$userRepo = App::make('Stark\Repositories\UserRepositoryInterface');
```

$userRepo is actually an EloquentUserRepository object now.

To understand how this works let&#8217;s flesh out our own IoC.

```php  
<?php
// IoC.php
class IoC {

    protected static $bindings = [];

    public static function bind($name, Callable $resolver)
    {
        static::$bindings[$name] = $resolver;
    }

    public static function make($name)
    {
        if (isset(static::$bindings[$name]))
        {
            $resolver = static::$registry[$name];

            return $resolver();
        }

        throw new Exception('Alias does not exist in the IoC registry.');
    }
}
```

This can be used in any PHP project outside of laravel. Check out this example.

```php  
<?php

include 'IoC.php';

class Truck {}

class Garage {
    protected $veh;

    public function __construct(Vehicle $veh)
    {
        $this->veh = $veh;
    }
}

IoC::bind('Vehicle', 'Truck')

$garage = new Garage(App::make('Vehicle'));
```

So we created a class Truck and a class Garage which depends on a Vehicle object. Then we used our homebrewed IoC to bind any call for Vehicle to a call for Truck instead. So when we created a new Garage we passed IoC::bind which would pass whatever we registered with IoC::bind. That&#8217;s all there is to it.

 [1]: http://www.edzynda.com/media/container.jpg
