---
author: Ed Zynda
categories:
- PHP
- AOP
- Web Development
- Testing
comments: true
date: 2016-04-26T00:00:00Z
published: true
share: true
tags:
- PHP
- AOP
- Aspect Oriented Programming
- Framework
- TDD
- Mocking
- Testing
title: Test The Untestable With AspectMock
url: /test-the-untestable-with-aspectmock/
---

Some of us have had the pleasure of working with someone else's ancient codebase to either add new features or fix bugs. It's never fun and usually a pain. Because of the design (or lack thereof) of the application, it doesn't lend itself to unit testing. The code is littered with static method calls, objects instantiated inside methods rather than being injected through a constructor, and other elements of poor design. This makes the codebase very fragile and any small change could break something if you aren't careful. How do we test the untestable? AspectMock to the rescue!

Using the power of AOP and the Go! AOP framework AspectMock allows you to create test doubles for almost anything anywhere in your code from public and private methods to static methods and even system functions like time().

Let's take a look at an example.

```php
<?php

class UserCreator
{
    public function create(array $data)
    {
        $user = new User($data);
        $user->save();
    }
}
```
Here we have a UserCreator class that depends on a User object to save a newly created user to the database. We'll pretend this works great but would like to have a test in place to avoid any future defects. We need to mock the User object somehow though because we don't want to make any calls to the database during our test.

```php
<?php
function testUserCreate()
{
    $user = Test::double('User', ['save' => null]));
    $service = new UserCreator;
    $service->create(['name' => 'Tony Stark']);
    $user->verifyInvoked('save');
}
```
Now when you run your test, AspectMock basically intercepts the call to the 'save' method and prevents it from touching the database.

You can even mock builtin PHP functions for greater flexibility.

```php
<?php
namespace demo;
test::func('demo', 'time', 'now');
$this->assertEquals('now', time());
```

AspectMock opens up a whole new world of possibilities when it comes to testing your legacy and even modern projects. Check out the project on [GitHub](https://github.com/Codeception/AspectMock "AspectMock") for more detailed documentation and give it a try in one your projects.
