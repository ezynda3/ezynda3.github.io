---
author: Ed Zynda
categories:
- Frameworks
- Laravel
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-15T00:00:00Z
share: true
tags:
- Blowfish
- Encryption
- laravel
- mcrypt
- NSA
- PHP
- self-destruct
title: Create A Self-destructing, Encrypted Message App In Laravel Part 1
url: /create-a-self-destructing-encrypted-message-app-in-laravel-part-1/
---

The Internet is the wild west these days. With hackers, scammers and even governments gathering our personal communications, you may have become pretty paranoid about what you post online these days. In response to all this paranoia, there have been several offerings from online applications to SaaS services claiming to improve your security and protect your data online. I wouldn't say that I fall into the paranoid category but security, anonymity and cryptography fascinate me.

While brain storming one day, I decided to go ahead and build a self-destructing, encrypted messaging application in Laravel, using PHP's built-in mcrypt library to see how hard it would be. Turns out it isn't very hard at all.

First we need to download Laravel and setup the base of our project. We'll call it 'Burnmsg'.

```bash   
$ composer create-project laravel/laravel burnmsg  
```

Now let's setup our composer.json file to fetch some dependencies. We'll use Twitter's bootstrap for styling and Jeffrey Way's time-saving artisan generators.

```json  
{
    "name": "laravel/laravel",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "require": {
        "laravel/framework": "4.0.*",
        "twitter/bootstrap": "3.0.*"
        "way/generators": "dev-master"
    },
    "autoload": {
        "classmap": [
            "app/commands",
            "app/controllers",
            "app/models",
            "app/database/migrations",
            "app/database/seeds",
            "app/tests/TestCase.php"
        ]
    },
    "scripts": {
        "post-install-cmd": [
            "php artisan optimize"
        ],
        "post-update-cmd": [
            "php artisan clear-compiled",
            "php artisan optimize"
        ],
        "post-create-project-cmd": [
            "php artisan key:generate"
        ]
    },
    "config": {
        "preferred-install": "dist"
    },
    "minimum-stability": "dev"
}
```

Make sure you add the generators service provider to 'app/config/app.php' in the 'providers' array.

```php
<?php
'providers' = array(
    '...',
    'Way\Generators\GeneratorsServiceProvider'
),
```

Change your database configuration in 'app/config/database.php'. Once that's done we can generate our model, controller and views all in one command using the useful generators tool.

```bash  
$ php artisan generate:resource --fields="body:binary,url:string,iv:binary,destroyed:boolean" messages
```

Now we can run our migration to create the messages table.

```bash  
$ php artisan migrate  
```

Next let's set up our routes. We only need a few. One for the message form. One to post the data and one to view our message.

```php  
<?php
Route::get('/', ['as' => 'messages.create', 'uses' => 'MessagesController@create']);
Route::post('/', ['as' => 'messages.store', 'uses' => 'MessagesController@store']);
Route::get('/{key}/{value}', ['as' => 'messages.show', 'uses' => 'MessagesController@show']);
```

In our controller we will only need a create, store and show method. The create method will return the view for displaying our form.

```php
<?php
public function create()
{
      return View::make('messages.create');
}
```

The store method will take the input and validate it. In this case we only care that the message isn't left blank. Next we will generate a random key. Then we will generate an IV or initialization vector. This is used to improve the randomization of our encryption. In this case we will use the Blowfish algorithm. The last steps are to encrypt the message and then save the encrypted message and the IV for decryption later. We will also create a random identifier. The key will not be saved though. Instead it will be part of the URL for retrieving the message later. Because the key is part of the URL the message creator needs to keep this as safe as possible. This also prevents the person running the server from being able to read any of the messages. Plausible-deniability. Finally we return the view which displays the URL for our message.

```php
<?php  
public function store()
{
        // Validate input
        $v = Message::validate(Input::all());

        if ($v !== true) {
            return Redirect::route('messages.create')
                ->withErrors($v->errors());
        }

        $msg = new Message;

        // Generate a key
        $key = sha1(microtime(true).mt_rand(10000,90000));

        // Generate an IV
        $iv_size = mcrypt_get_iv_size(MCRYPT_BLOWFISH, MCRYPT_MODE_CFB);
        $iv = mcrypt_create_iv($iv_size, MCRYPT_DEV_URANDOM);

        // Encrypt the message
        $body = mcrypt_encrypt(MCRYPT_BLOWFISH, $key, Input::get('body'), MCRYPT_MODE_CFB, $iv);

        // Save the message to the database
        $msg->body = $body;
        $msg->url = Message::get_unique_url();
        $msg->iv = $iv;
        $msg->save();

        // Return the view
        return View::make('messages.store', ['url' => $msg->url, 'key' => $key]);
}
```

The show method will take the random identifier and the key and decrypt the message. Once decrypted it will overwrite the message with 'null' in the database and return the view displaying the one-time-viewable message.

```php
<?php
public function show($url, $key)
{
        // Fetch our message
        $msg = Message::where('url', '=', $url)->first();

        if ($msg->destroyed) {
            $body = "This message has been destroyed";
        } else {
            // Decrypt it
            $iv = $msg->iv;
            $body = mcrypt_decrypt(MCRYPT_BLOWFISH, $key, $msg->body, MCRYPT_MODE_CFB, $iv);

            // Destroy the message
            $msg->body = null;
            $msg->destroyed = true;
            $msg->save();
        }

        return View::make('messages.show', ['body' => $body]);
}
```

Now let's take care of our model. The model will handle the validation as well as generating the random url identifier saved in the store method above.

```php  
<?php

class Message extends Eloquent {
    protected $guarded = ['id', 'created_at', 'updated_at'];

    public static $rules = ['body' => 'required'];

    public static function validate($input)
    {
        $v = Validator::make($input, static::$rules);
        return $v->fails() ? $v : true;
    }

    public static function  get_unique_url() {

         // set a random number
        $number = rand(10000, 9999999);

                // character list for generating a random string
        $charlist = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";

        $decimal = intval($number);

        //get the list of valid characters
        $charlist = substr($charlist, 0, 62);

        $converted = '';

        while($number > 0) {
            $converted = $charlist{($number % 62)} . $converted;
            $number = floor($number / 62);
        }

        if( static::whereUrl($converted)->first() ) {
            static::get_unique_url();
        }

                return $converted;
    }
}
```

Now we have most of the hard part out of the way. All that is left is to create and style the various views using Twitter Bootstrap which will be covered in part 2.

 [1]: http://www.edzynda.com/media/self-destruct.jpg
