---
title: Working With Queues Using PHP And Beanstalkd
author: Ed Zynda
layout: post
permalink: /working-with-queues-using-php-and-beanstalkd/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dsq_thread_id:
  - 1996679882
categories:
  - PHP
  - Tutorials
  - Web Development
tags:
  - beanstalkd
  - pheanstalk
  - PHP
  - queues
---
[<img src="http://www.edzynda.com/media/beanstalk-242x300.jpg" alt="Beanstalk" width="242" height="300" class="alignleft size-medium wp-image-578" />][1]  
PHP is often considered the glue of the Internet because it&#8217;s can be used for a lot of different things. There are so many built in functions and features that you are unlikely to use close to all of them if you work with PHP the rest of your life. This is great because we can solve various problems in our web apps without resorting to bringing in another language. Sometimes though, our application logic can get quite large or resource intensive. This translates into slow response times for the users of our apps if certain operations are tied into user events.

<!--more-->

[<img src="http://www.edzynda.com/media/beanstalk-242x300.jpg" alt="Beanstalk" width="242" height="300" class="alignleft size-medium wp-image-578" />][1]  
PHP is often considered the glue of the Internet because it&#8217;s can be used for a lot of different things. There are so many built in functions and features that you are unlikely to use close to all of them if you work with PHP the rest of your life. This is great because we can solve various problems in our web apps without resorting to bringing in another language. Sometimes though, our application logic can get quite large or resource intensive. This translates into slow response times for the users of our apps if certain operations are tied into user events.

Let&#8217;s say we&#8217;re running dating site. Users are asked to enter a bunch of information at sign-up. This information will then be used by our application to match the new user with existing users and then email or send them a message with their new matches. This can potentially be a lengthy operation. If we run this as soon as the user hits submit they could be greeted by a long wait for the page or even worse a timeout error. To prevent this we can use a queue.

In this example we&#8217;ll be using a simple messaging queue called <a href="http://kr.github.io/beanstalkd/" title="Beanstalkd" target="_blank">Beanstalkd</a> and a PHP wrapper package called <a href="https://github.com/pda/pheanstalk/" title="Pheanstalk" target="_blank">Pheanstalk</a> to interact with it.

First install Beanstalkd using whatever is the norm in your current linux distribution whether that is yum or apt-get. You can also clone the github repo and compile from source. Once installed you can run Beanstalkd locally from the command line.

[sh]  
$ beanstalkd -l 127.0.0.1  
[/sh]

Next let&#8217;s pull in Pheanstalk. Create a composer.json file.

[js]  
{  
&#8220;require&#8221;: {  
&#8220;pda/pheanstalk&#8221;: &#8220;2.1.0&#8243;  
}  
}  
[/js]

Run &#8216;composer update&#8217;. Now let&#8217;s create a file called &#8216;functions.php&#8217; to hold our super intensive matching function. This will be used by our worker script.

[php]  
<?php</p> 

function find_matches($data) {  
// Run super awesome calculations  
// Send user our email  
}  
[/php]

Now let's place a job on the queue. We'll call this file 'find_matches.php'.

[php]  
<?php</p> 

include 'vendor/autoload.php';

$queue = new Pheanstalk_Pheanstalk('127.0.0.1');

$job = new stdClass();  
// the function to run  
$job->function = &#8216;find_matches&#8217;;  
// our user entered data  
$job->user\_data = $\_POST;

// beanstalkd uses strings so we json_encode our job for storage  
$job\_data = json\_encode($job);

// place our job into the queue into a tube we&#8217;ll call matching  
$queue->useTube(&#8216;matching&#8217;)  
->put($job_data);

[/php]

Now our job is in the queue waiting for a worker to come along and grab it. Let&#8217;s create one in &#8216;worker.php&#8217;.

[php]  
<?php<br /> include 'vendor/autoload.php';

  
include 'functions.php';

$queue = new Pheanstalk_Pheanstalk('127.0.0.1');

while (1) {  
// grab the next job off the queue and reserve it  
$job = $queue->watch(&#8216;matching&#8217;)  
->ignore(&#8216;default&#8217;)  
->reserve();

// decode the json data  
$job\_data = json\_decode($job->getData(), false);

$function = $job_data->function;  
$data = $job\_data->user\_data;

// run the function  
$function($data);

// remove the job from the queue  
$queue->delete($job);  
}  
[/php]

Now the worker script will not run automatically so you will need to either run it as a cron job or use a tool like daemontools to keep it running all the time in the background.

This is just a simple example but hopefully it gives you an idea of what you can accomplish with queues.

 [1]: http://www.edzynda.com/media/beanstalk.jpg