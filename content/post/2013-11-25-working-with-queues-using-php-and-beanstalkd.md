---
author: Ed Zynda
categories:
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-25T00:00:00Z
share: true
tags:
- beanstalkd
- pheanstalk
- PHP
- queues
title: Working With Queues Using PHP And Beanstalkd
url: /working-with-queues-using-php-and-beanstalkd/
---

PHP is often considered the glue of the Internet because it's can be used for a lot of different things. There are so many built in functions and features that you are unlikely to use close to all of them if you work with PHP the rest of your life. This is great because we can solve various problems in our web apps without resorting to bringing in another language. Sometimes though, our application logic can get quite large or resource intensive. This translates into slow response times for the users of our apps if certain operations are tied into user events.

Let's say we're running dating site. Users are asked to enter a bunch of information at sign-up. This information will then be used by our application to match the new user with existing users and then email or send them a message with their new matches. This can potentially be a lengthy operation. If we run this as soon as the user hits submit they could be greeted by a long wait for the page or even worse a timeout error. To prevent this we can use a queue.

In this example we'll be using a simple messaging queue called <a href="http://kr.github.io/beanstalkd/" title="Beanstalkd" target="_blank">Beanstalkd</a> and a PHP wrapper package called <a href="https://github.com/pda/pheanstalk/" title="Pheanstalk" target="_blank">Pheanstalk</a> to interact with it.

First install Beanstalkd using whatever is the norm in your current linux distribution whether that is yum or apt-get. You can also clone the github repo and compile from source. Once installed you can run Beanstalkd locally from the command line.

```bash
$ beanstalkd -l 127.0.0.1  
```

Next let's pull in Pheanstalk. Create a composer.json file.

```javascript  
{  
    "require": {  
        "pda/pheanstalk": "2.1.0"  
    }  
}  
```

Run 'composer update'. Now let's create a file called 'functions.php' to hold our super intensive matching function. This will be used by our worker script.

```php  
<?php

function find_matches($data) {  
    // Run super awesome calculations  
    // Send user our email  
}  
```

Now let's place a job on the queue. We'll call this file 'find_matches.php'.

```php  
<?php

include 'vendor/autoload.php';

$queue = new Pheanstalk_Pheanstalk('127.0.0.1');

$job = new stdClass();  
// the function to run  
$job->function = 'find_matches';  
// our user entered data  
$job->user_data = $_POST;

// beanstalkd uses strings so we json_encode our job for storage  
$job_data = json_encode($job);

// place our job into the queue into a tube we'll call matching  
$queue->useTube('matching')  
    ->put($job_data);
```

Now our job is in the queue waiting for a worker to come along and grab it. Let's create one in 'worker.php'.

```php  
<?php
include 'vendor/autoload.php';
include 'functions.php';

$queue = new Pheanstalk_Pheanstalk('127.0.0.1');

while (true) {  
    // grab the next job off the queue and reserve it  
    $job = $queue->watch('matching')  
        ->ignore('default')  
        ->reserve();

    // decode the json data  
    $job_data = json_decode($job->getData(), false);

    $function = $job_data->function;  
    $data = $job_data->user_data;

    // run the function  
    $function($data);

    // remove the job from the queue  
    $queue->delete($job);  
}  
```

Now the worker script will not run automatically so you will need to either run it as a cron job or use a tool like daemontools to keep it running all the time in the background.

This is just a simple example but hopefully it gives you an idea of what you can accomplish with queues.

 [1]: http://www.edzynda.com/media/beanstalk.jpg
