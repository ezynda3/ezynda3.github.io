---
author: Ed Zynda
categories:
- Frameworks
- Laravel
- Tutorials
- Web Development
comments: true
date: 2013-10-18T00:00:00Z
share: true
tags:
- dependency injection
- interface
- IoC
- laravel
- PHP
- repository
- TDD
title: Using Repositories In Laravel
url: /using-repositories-in-laravel/
---

The past couple of days I have been cramming my brain with coding knowledge. I&#8217;m working on a side project that I&#8217;ll likely announce in detail as it gets closer to launch. Since I&#8217;m starting from an empty Laravel project and building it from the ground up, I&#8217;m learning quite a bit about the framework and even some of the new features in PHP 5.4 and above.

One technique I&#8217;m starting to better understand is that of dependency injection. Dependency injection sounds scary at first but it&#8217;s really a simple concept and has the potential to improve the flexibility of your projects. Laravel allows you to take dependency injection a step further with it&#8217;s powerful IoC container.

If you&#8217;ve frequented web development forums or IRC channels lately, you might hear seasoned veterans blabbing about the merits of TDD or Test Driven Development. It&#8217;s another concept that sounds scary at first but really isn&#8217;t once you start digging into it. Basically TDD preaches that you should write automated tests for your code **before** you even write a single line of production code. Not only that, you should be writing tests that work with a single component at a time, in isolation. If you follow the single responsibility principle when writing code, this shouldn&#8217;t be too hard.

What if your class or controller methods depend on another class that has it&#8217;s own responsibilities? Theoretically those classes should have their own tests and should be isolated from your own tests. How do we do this though? The answer is dependency injection.

Dependency injection is simply passing the classes your current class depends on through the constructor.

```php  
<?php
class UserController extends BaseController {
    protected $dependency;

    public function __construct(Dependency $dependency)
    {
        $this->dependency = $dependency;
    }
}
```

That&#8217;s it. Not too scary right? This way, when you write your automated tests, you can now &#8216;mock&#8217; those classes with a tool like Mockery and inject them from inside your test code. If those dependencies accessed a database or live filesystem you can rest assured your tests will not because those dependencies are now mocked or stubbed.

So dependency injection is great for testing but it has other benefits. Let&#8217;s say in laravel you have a UserController. That controller has a method that fetches all users and sends them to a view.

```php
<?php
class UserController extends BaseController {

    public function index()
    {
        $users = User::all();
        return View::make('users.list', compact($users));
    }
}
```

It's very simple to call our User model and just call methods from within the controller. But this will create a nightmare for testing and isn't very flexible. Why not inject the User model?

```php  
<?php
class UserController extends BaseController {
    protected $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }
    public function index()
    {
        $users $this->user->all();
        return View::make('users.list', compact($users));
    }
}
```

Okay that&#8217;s great but say one day you decide to go with a different implementation of your users like MongoDB or a flatfile database? You&#8217;ll have to go through your code and clean out and refactor anything that references the Laravel models because they extend Eloquent which uses an SQL database. This is where the idea of using repositories comes in.

First let&#8217;s create an interface. Think of an interface as a contract. Any class that implements this interface must also implement its methods.

```php  
<?php namespace Stark\Repositories;

interface UserRepositoryInterface {
    public function getAll();
}
```

We'll store it in a custom directory called 'Stark/Repositories'. Notice that the namespace goes along with the directory structure. Now let's create a class that implements this interface.

```php  
<?php namespace Stark\Repositories;

use User;

class EloquentUserRepository implements UserRepositoryInterface {
    public function getAll()
    {
        return User::all();
    }
}
```

Cool. Now let's just refactor our controller to use our interface.

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

Now remember that interfaces are cannot be instantiated but Laravel is smart enough to inject a proper class in it&#8217;s place if you give it some hints. First we have to make sure our &#8216;Stark&#8217; namespace is autoloaded. For that we edit our composer.json file and add the namespace and it&#8217;s location under &#8220;psr-0&#8243;.

```json  
{
    "autoload": {
        "psr-0": {
            "Stark": "app/"
        }
    }
}
```

Next we tell Laravel that anytime we reference UserRepositoryInterface we really want EloquentUserRepository. We can do that in our &#8216;app/routes.php&#8217; file.

```php
<?php
App::bind('Stark\Repositories\UserRepositoryInterface', 'Stark\Repositories\EloquentUserRepository');
```

Now if we decide to use some other storage method we can simply code another repository that implements the UserRepositoryInterface and change that one line in the routes file to reflect the change. The rest of your app couldn&#8217;t care less.

 [1]: http://www.edzynda.com/media/repository.jpg
