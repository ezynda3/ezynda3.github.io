---
title: Using Faker To Populate Your Laravel Projects During Development
author: Ed Zynda
layout: post
permalink: /using-faker-to-populate-your-laravel-projects-during-development/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
categories:
  - Laravel
  - PHP
  - Tutorials
  - Uncategorized
  - Web Development
tags:
  - database
  - Faker
  - laravel
  - PHP
  - seeder
---
[<img src="http://www.edzynda.com/media/fake-300x162.jpg" alt="Fake" width="300" height="162" class="alignnone size-medium wp-image-625" />][1]  
I&#8217;m a very visual person so when I&#8217;m developing something, I like see and interact with my projects exactly how I would if it was in the production stage. The problem is, I don&#8217;t have a handful of test users on call to register on my test application and start using it. Laravel already has an easy way to populate your databases with users and other data but there&#8217;s still some manual data entry involved. We can eliminate this with a cool library called <a href="https://github.com/fzaninotto/Faker" title="Faker on GitHub" target="_blank">Faker</a>.

<!--more-->

[<img src="http://www.edzynda.com/media/fake-300x162.jpg" alt="Fake" width="300" height="162" class="alignnone size-medium wp-image-625" />][1]  
I&#8217;m a very visual person so when I&#8217;m developing something, I like see and interact with my projects exactly how I would if it was in the production stage. The problem is, I don&#8217;t have a handful of test users on call to register on my test application and start using it. Laravel already has an easy way to populate your databases with users and other data but there&#8217;s still some manual data entry involved. We can eliminate this with a cool library called <a href="https://github.com/fzaninotto/Faker" title="Faker on GitHub" target="_blank">Faker</a>.

Faker takes all the manual entry out of database seeding. It can generate anything from usernames, email addresses and city names to dates and fake credit card numbers. This saves loads of time when creating your database seeder. If you&#8217;re new to Laravel or have no idea how to seed your database, check out the awesome Laravel <a href="http://laravel.com/docs/migrations#database-seeding" title="Laravel - Database Seeding" target="_blank">documentation</a> on the subject.

To get started just add Faker using composer.

[sh]  
$ composer require fzaninotto/faker  
[/sh]

When prompted for the version I entered &#8217;1.4.*@dev&#8217; which is the latest at the time of this writing. This will add faker to &#8216;comoposer.json&#8217; and download the library. Laravel will autoload the library for you.

I have a travel buddy meetup site, that I&#8217;m creating. Users can sign-up and then create trips. Other users can see these trips and then propose a meetup or just give helpful tips and advice. Let&#8217;s see what my seeders will look like using Faker.

Here&#8217;s the user seeder.

[php]  
<?php</p> 

class UserTableSeeder extends Seeder {

public function run()  
{  
$faker = Faker\Factory::create();

User::truncate();

foreach(range(1,30) as $index)  
{  
User::create([  
'username' => str_replace(&#8216;.&#8217;, &#8221;, $faker->unique()->userName),  
&#8216;email&#8217; => $faker->email,  
&#8216;password&#8217; => &#8216;password&#8217;,  
&#8216;confirmation&#8217; => str_random(64),  
&#8216;confirmed&#8217; => true  
]);  
}

}

}  
[/php]

First I created a new instance of Faker using &#8216;$faker = Faker\Factory::create();&#8217;. &#8216;User::truncate();&#8217; just clears the database out so we can reseed it. Next I create a loop that will create 30 new users. For any field that requires fake data just call the Faker instance we created up top and the type of data we need (e.g &#8216;$faker->userName). Faker likes to add a &#8216;.&#8217; to some usernames which doesn&#8217;t play nice with my app which is why I used &#8216;str_replace()&#8217; to get rid of those. You can even use the &#8216;unique()&#8217; modifier to make sure usernames aren&#8217;t repeated.

As you can see, it&#8217;s pretty straight forward. Let&#8217;s look at another example. Here I have my trip seeder.

[php]  
<?php</p> 

class TripTableSeeder extends Seeder {

public function run()  
{

$faker = Faker\Factory::create();

Trip::truncate();

foreach(range(1,10) as $index)  
{  
Trip::create([  
'user_id' => rand(1,30),  
&#8216;location&#8217; => $faker->city . &#8216;, &#8216; . $faker->country,  
&#8216;from&#8217; => $from = $faker->dateTimeBetween(&#8216;+1 days&#8217;, &#8216;+2 years&#8217;)->format(&#8216;m/d/Y&#8217;),  
&#8216;to&#8217; => $faker->dateTimeBetween($from, $from . &#8216; +3 months&#8217;)->format(&#8216;m/d/Y&#8217;),  
&#8216;note&#8217; => $faker->paragraph(1)  
]);  
}  
}  
}  
[/php]

Same principle as before. Faker can create dates in a specific range, words, sentences and even paragraphs using the popular &#8216;lorem ipsum&#8217; filler.

This is a great tool for projects that will be working with a lot of users and data. For more information on Faker&#8217;s features, check out the <a href="https://github.com/fzaninotto/Faker" title="Faker on GitHub" target="_blank">GitHub repo</a>.

 [1]: http://www.edzynda.com/media/fake.jpg