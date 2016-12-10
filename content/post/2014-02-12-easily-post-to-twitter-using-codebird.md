---
author: Ed Zynda
categories:
- PHP
- Tutorials
- Web Development
comments: true
date: 2014-02-12T00:00:00Z
share: true
tags:
- api
- codebird-php
- Composer
- PHP
- Twitter
title: Easily Post To Twitter Using Codebird
url: /easily-post-to-twitter-using-codebird/
---

Recently I needed to add some social media functionality to a project. I needed to be able to post to Twitter and Facebook whenever a user created a new item in the web app. I had never done this before in PHP because normally I would use some sort of plugin in WordPress to do this. Since this was not a WordPress site, I needed to do it by hand. Luckily this isn&#8217;t as hard as you might think.

The first thing you need is a Twitter account to post to. Second, you need to create and register your application with Twitter.

![Create your app](http://www.edzynda.com/media/twitter_post_app_1.jpg)

Once you've successfully created your app, click on the the API Keys tab. You will need to copy down your API Key and API Secret. Next you will need to change the permissions of this app. Click on &#8220;modify app permissions&#8221; and change it to read and write.

Last you will need to create an access token. This is what will allow your app to post on your Twitter account&#8217;s behalf. Click &#8220;Generate my Access Token&#8221; and then copy down the Access token and Access token secret.

![Grab your tokens](http://www.edzynda.com/media/twitter_post_app_2.jpg)

Now that we have an app registered with Twitter we can get to the code.

In this example we will use a library called Codebird. This is an excellent library with excellent documentation. You can check out the project on [Github](https://github.com/jublonet/codebird-php).

Use composer to install Codebird

```bash
$ composer require jublonet/codebird-php
```

When prompted enter 2.4.1 for the version.

Now we can create a simple twitter class.

```php
<?php
require 'vendor/autoload.php';

use Codebird\Codebird;

class Twitter
{
    protected $consumer_key = <API_KEY>;
    protected $consumer_secret = <API_SECRET>;
    protected $access_token = <ACCESS_TOKEN>;
    protected $access_secret = <ACCESS_SECRET>;
    protected $twitter;

    public function __construct()
    {
        // Fetch new Twitter Instance
        Codebird::setConsumerKey($this->consumer_key, $this->consumer_secret);
        $this->twitter = Codebird::getInstance();

        // Set access token
        $this->twitter->setToken($this->access_token, $this->access_secret);
    }

    public function tweet($message)
    {
        return $this->twitter->statuses_update(['status' => $message]);
    }

}
```

Replace the protected variables in &#8216;< >&#8216; brackets with your own app&#8217;s information. In the constructor we simply set our consumer key then fetch an instance of Codebird. Once we have a fresh instance we can set our access token.

Next we create a simple method with accepts a single argument which is simply the message you would like to tweet.

Now when we want to tweet we can do something like the following.

```php
<?php

require 'Twitter.php';

$twtr = new Twitter;

$twtr->tweet('Hello World!');
```

It's really that simple. Of course there is a lot more you can do with this like post images for example but this is just to show you how easy it can be. For more features just check out the documentation on the Codebird [Github](https://github.com/jublonet/codebird-php) page.
