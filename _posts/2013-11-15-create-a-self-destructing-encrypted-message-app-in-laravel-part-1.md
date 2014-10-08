---
title: Create A Self-destructing, Encrypted Message App In Laravel Part 1
author: Ed Zynda
layout: post
permalink: /create-a-self-destructing-encrypted-message-app-in-laravel-part-1/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dsq_thread_id:
  - 1958225084
categories:
  - Frameworks
  - Laravel
  - PHP
  - Tutorials
  - Web Development
tags:
  - Blowfish
  - Encryption
  - laravel
  - mcrypt
  - NSA
  - PHP
  - self-destruct
---
[<img src="http://www.edzynda.com/media/self-destruct-300x239.jpg" alt="This Message Will Self-destruct" width="300" height="239" class="alignright size-medium wp-image-564" />][1]  
The Internet is the wild west these days. With hackers, scammers and even governments gathering our personal communications, you may have become pretty paranoid about what you post online these days. In response to all this paranoia, there have been several offerings from online applications to SaaS services claiming to improve your security and protect your data online. I wouldn&#8217;t say that I fall into the paranoid category but security, anonymity and cryptography fascinate me. 

While brain storming one day, I decided to go ahead and build a self-destructing, encrypted messaging application in Laravel, using PHP&#8217;s built-in mcrypt library to see how hard it would be. Turns out it isn&#8217;t very hard at all.

<!--more-->

  
[<img src="http://www.edzynda.com/media/self-destruct-300x239.jpg" alt="This Message Will Self-destruct" width="300" height="239" class="alignright size-medium wp-image-564" />][1]  
The Internet is the wild west these days. With hackers, scammers and even governments gathering our personal communications, you may have become pretty paranoid about what you post online these days. In response to all this paranoia, there have been several offerings from online applications to SaaS services claiming to improve your security and protect your data online. I wouldn&#8217;t say that I fall into the paranoid category but security, anonymity and cryptography fascinate me. 

While brain storming one day, I decided to go ahead and build a self-destructing, encrypted messaging application in Laravel, using PHP&#8217;s built-in mcrypt library to see how hard it would be. Turns out it isn&#8217;t very hard at all.

First we need to download Laravel and setup the base of our project. We&#8217;ll call it &#8216;Burnmsg&#8217;.

[sh]  
$ composer create-project laravel/laravel burnmsg  
[/sh]

Now let&#8217;s setup our composer.json file to fetch some dependencies. We&#8217;ll use Twitter&#8217;s bootstrap for styling and Jeffrey Way&#8217;s time-saving artisan generators.

[js]  
{  
&#8220;name&#8221;: &#8220;laravel/laravel&#8221;,  
&#8220;description&#8221;: &#8220;The Laravel Framework.&#8221;,  
&#8220;keywords&#8221;: ["framework", "laravel"],  
&#8220;license&#8221;: &#8220;MIT&#8221;,  
&#8220;require&#8221;: {  
&#8220;laravel/framework&#8221;: &#8220;4.0.*&#8221;,  
&#8220;twitter/bootstrap&#8221;: &#8220;3.0.*&#8221;  
&#8220;way/generators&#8221;: &#8220;dev-master&#8221;  
},  
&#8220;autoload&#8221;: {  
&#8220;classmap&#8221;: [  
"app/commands",  
"app/controllers",  
"app/models",  
"app/database/migrations",  
"app/database/seeds",  
"app/tests/TestCase.php"  
]  
},  
&#8220;scripts&#8221;: {  
&#8220;post-install-cmd&#8221;: [  
"php artisan optimize"  
],  
&#8220;post-update-cmd&#8221;: [  
"php artisan clear-compiled",  
"php artisan optimize"  
],  
&#8220;post-create-project-cmd&#8221;: [  
"php artisan key:generate"  
]  
},  
&#8220;config&#8221;: {  
&#8220;preferred-install&#8221;: &#8220;dist&#8221;  
},  
&#8220;minimum-stability&#8221;: &#8220;dev&#8221;  
}  
[/js]

Make sure you add the generators service provider to &#8216;app/config/app.php&#8217; in the &#8216;providers&#8217; array.

[php]  
&#8216;providers&#8217; = array(  
&#8216;&#8230;&#8217;,  
&#8216;Way\Generators\GeneratorsServiceProvider&#8217;  
),  
[/php]

Change your database configuration in &#8216;app/config/database.php&#8217;. Once that&#8217;s done we can generate our model, controller and views all in one command using the useful generators tool.

[sh]  
$ php artisan generate:resource &#8211;fields=&#8221;body:binary,url:string,iv:binary,destroyed:boolean&#8221; messages  
[/sh]

Now we can run our migration to create the messages table.

[sh]  
$ php artisan migrate  
[/sh]

Next let&#8217;s set up our routes. We only need a few. One for the message form. One to post the data and one to view our message.

[php]  
<?php<br /> Route::get('/', ['as' => &#8216;messages.create&#8217;, &#8216;uses&#8217; => &#8216;MessagesController@create&#8217;]);

  
Route::post(&#8216;/&#8217;, ['as' => 'messages.store', 'uses' => 'MessagesController@store']);  
Route::get(&#8216;/{key}/{value}&#8217;, ['as' => 'messages.show', 'uses' => 'MessagesController@show']);  
[/php]

In our controller we will only need a create, store and show method. The create method will return the view for displaying our form.

[php]  
public function create()  
{  
return View::make(&#8216;messages.create&#8217;);  
}  
[/php]

The store method will take the input and validate it. In this case we only care that the message isn&#8217;t left blank. Next we will generate a random key. Then we will generate an IV or initialization vector. This is used to improve the randomization of our encryption. In this case we will use the Blowfish algorithm. The last steps are to encrypt the message and then save the encrypted message and the IV for decryption later. We will also create a random identifier. The key will not be saved though. Instead it will be part of the URL for retrieving the message later. Because the key is part of the URL the message creator needs to keep this as safe as possible. This also prevents the person running the server from being able to read any of the messages. Plausible-deniability. Finally we return the view which displays the URL for our message.

[php]  
public function store()  
{  
// Validate input  
$v = Message::validate(Input::all());

if ($v !== true) {  
return Redirect::route(&#8216;messages.create&#8217;)  
->withErrors($v->errors());  
}

$msg = new Message;

// Generate a key  
$key = sha1(microtime(true).mt_rand(10000,90000));

// Generate an IV  
$iv\_size = mcrypt\_get\_iv\_size(MCRYPT\_BLOWFISH, MCRYPT\_MODE_CFB);  
$iv = mcrypt\_create\_iv($iv\_size, MCRYPT\_DEV_URANDOM);

// Encrypt the message  
$body = mcrypt\_encrypt(MCRYPT\_BLOWFISH, $key, Input::get(&#8216;body&#8217;), MCRYPT\_MODE\_CFB, $iv);

// Save the message to the database  
$msg->body = $body;  
$msg->url = Message::get\_unique\_url();  
$msg->iv = $iv;  
$msg->save();

// Return the view  
return View::make(&#8216;messages.store&#8217;, ['url' => $msg->url, 'key' => $key]);  
}  
[/php]

The show method will take the random identifier and the key and decrypt the message. Once decrypted it will overwrite the message with &#8216;null&#8217; in the database and return the view displaying the one-time-viewable message.

[php]  
public function show($url, $key)  
{  
// Fetch our message  
$msg = Message::where(&#8216;url&#8217;, &#8216;=&#8217;, $url)->first();

if ($msg->destroyed) {  
$body = &#8220;This message has been destroyed&#8221;;  
} else {  
// Decrypt it  
$iv = $msg->iv;  
$body = mcrypt\_decrypt(MCRYPT\_BLOWFISH, $key, $msg->body, MCRYPT\_MODE\_CFB, $iv);

// Destroy the message  
$msg->body = null;  
$msg->destroyed = true;  
$msg->save();  
}

return View::make(&#8216;messages.show&#8217;, ['body' => $body]);  
}  
[/php]

Now let&#8217;s take care of our model. The model will handle the validation as well as generating the random url identifier saved in the store method above.

[php]  
<?php</p> 

class Message extends Eloquent {  
protected $guarded = ['id', 'created\_at', 'updated\_at'];

public static $rules = ['body' => &#8216;required&#8217;];

public static function validate($input)  
{  
$v = Validator::make($input, static::$rules);  
return $v->fails() ? $v : true;  
}

public static function get\_unique\_url() {

// set a random number  
$number = rand(10000, 9999999);

// character list for generating a random string  
$charlist = &#8220;0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ&#8221;;

$decimal = intval($number);

//get the list of valid characters  
$charlist = substr($charlist, 0, 62);

$converted = &#8221;;

while($number > 0) {  
$converted = $charlist{($number % 62)} . $converted;  
$number = floor($number / 62);  
}

if( static::whereUrl($converted)->first() ) {  
static::get\_unique\_url();  
}

return $converted;  
}  
}  
[/php]

Now we have most of the hard part out of the way. All that is left is to create and style the various views using Twitter Bootstrap which will be covered in part 2.

 [1]: http://www.edzynda.com/media/self-destruct.jpg