---
author: Ed Zynda
categories:
- PHP
- Tutorials
- Web Development
- Continuous Integration
comments: true
date: 2015-03-20T00:00:00Z
share: true
tags:
- PHP
- CI
- Jenkins
- PHPSpec
- Robo
title: Continuous Integration With Jenkins Part II
url: /continuous-integration-with-jenkins-part-ii/
---

In the [first part](/continuous-integration-with-jenkins-part-ii/)  of this series, I showed you how you can easily set up a project to run automated tests using Jenkins. The quickest and easiest way to build your project and run tests is to add a build step that runs a bunch of shell commands. This is fine for small projects but what about larger projects with complex build steps and dependencies?

A better options is to use another tool to automate the build process and perform certain tasks dynamically. There are quite a lot of tools out there to do this. A popular tool used in Jenkins setups is [Apache Ant](http://ant.apache.org/) . It's used a lot by Java developers to automate builds of their applications but can also be used to build and run tasks for just about anything.

I was planning to do this article about Ant but decided against it. While Ant is a very powerful tool, it's configuration is in XML which is just gross. Don't get me wrong, XML has it's uses but I prefer something more readable. Instead, I found a PHP based tool called [Robo](http://robo.li)  which, in my opinion, is easier on the eyes and can be just as powerful.

If you still want to try out Ant you can check out a great example build file [here](http://jenkins-php.org/automation.html). Don't say I didn't warn you though.

So what is robo? Here is what they say on their website.

*"Robo is a task runner you always have been looking for. It allows you to write fully customizable tasks in common OOP PHP style. Robo has comprehensive list of built-in common tasks for development, testing, and deployment."*

Installing and running robo tasks is really simple. It can be installed using Composer.

```sh
$ composer require codegyre/robo
```

Once installed just run the command.

```sh
$ vendor/bin/robo
```

If it's your first time running, it will create a file called 'RoboFile.php'. This is the file we will use to define our commands and run our tasks.

Let's create a RoboFile for building and running some tests for a project.

```php
<?php
class RoboFile extends \Robo\Tasks
{
    // Run ALL the tasks
    public function build()
    {
        $this->setup();
        $this->test();
        $this->lint();
        $this->codesniffer();
    }

    // Setup
    private function setup()
    {
        if (! is_dir('reports')) {
            $this->taskFilesystemStack()
                ->mkdir('reports')
                ->run();
        }

        $this->_cleanDir('reports');
    }

    // Run PHPSpec tests
    public function test()
    {
        $this->taskPhpspec()
            ->format('html')
            ->arg('> reports/phpspec.html')
            ->run();
    }

    // Check for syntax errors
    public function lint()
    {
        $this->taskExec('php')
            ->arg('-l')
            ->arg('spec/*/*')
            ->run();

        $this->taskExec('php')
            ->arg('-l')
            ->arg('src/*')
            ->run();
    }

    // Check coding style
    public function codesniffer()
    {
        $this->taskExec('vendor/bin/phpcs')
            ->arg('--report=checkstyle')
            ->arg('--report-file=reports/checkstyle.xml')
            ->arg('--standard=PSR2')
            ->arg('--extensions=php')
            ->arg('src')
            ->run();
    }
}

```

Each public method in the RoboFile class is interprented as a command. For example to run the lint command, which checks our code for syntax errors, we can run this.

```sh
$ vendor/bin/robo lint
```

Each command can run one or more tasks. Robo comes with a handful of built in tasks to include running PHPUnit and PHPSpec tests. If there's a task that robo doesn't have, you can use the 'taskExec' task and just give it the command like you would on the command line. Each task has an 'arg' method which just tacks on the various arguments to the command. The 'run' method is pretty self explanatory.

Because robo runs on PHP you can add all kinds of logic to run tasks only when certain conditions are met. I find the syntax and fluent interface to be much more readable than XML as well.

Now that we have a sweet build script we can simplify our config in Jenkins a little bit. Under your project's config in Jenkin's dashboard, in our 'Execute Shell' build step we can add the following.

```sh
/usr/local/bin/composer --prefer-source install
vendor/bin/robo build
```

That's it! The script will go ahead and create a reports directory for published reports. It will run our tests, check for syntax and even do a code standard check against PSR-2. If you want to try it for yourself, you can grab this sample project I created [here](https://github.com/ezynda3/string-calculator).

You can also check out Robo's excellent documentation [here](http://robo.li/started/).
