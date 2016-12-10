---
author: Ed Zynda
categories:
- Laravel
- PHP
- Tutorials
- Uncategorized
- Web Development
comments: true
date: 2013-12-20T00:00:00Z
share: true
tags:
- database
- Faker
- laravel
- PHP
- seeder
title: Using Faker To Populate Your Laravel Projects During Development
url: /using-faker-to-populate-your-laravel-projects-during-development/
---

I'm a very visual person so when I'm developing something, I like see and interact with my projects exactly how I would if it was in the production stage. The problem is, I don't have a handful of test users on call to register on my test application and start using it. Laravel already has an easy way to populate your databases with users and other data but there's still some manual data entry involved. We can eliminate this with a cool library called <a href="https://github.com/fzaninotto/Faker" title="Faker on GitHub" target="_blank">Faker</a>.

Faker takes all the manual entry out of database seeding. It can generate anything from usernames, email addresses and city names to dates and fake credit card numbers. This saves loads of time when creating your database seeder. If you're new to Laravel or have no idea how to seed your database, check out the awesome Laravel <a href="http://laravel.com/docs/migrations#database-seeding" title="Laravel - Database Seeding" target="_blank">documentation</a> on the subject.

To get started just add Faker using composer.

```bash  
$ composer require fzaninotto/faker  
```

When prompted for the version I entered '1.4.*@dev' which is the latest at the time of this writing. This will add faker to 'comoposer.json' and download the library. Laravel will autoload the library for you.

I have a travel buddy meetup site, that I'm creating. Users can sign-up and then create trips. Other users can see these trips and then propose a meetup or just give helpful tips and advice. Let's see what my seeders will look like using Faker.

Here's the user seeder.

```php  
<?php
class UserTableSeeder extends Seeder
{

    public function run()  
    {  
        $faker = Faker\Factory::create();

        User::truncate();

        foreach(range(1,30) as $index)  
        {  
            User::create([  
                'username' => str_replace('.', '_', $faker->unique()->userName),  
                'email' => $faker->email,  
                'password' => 'password',  
                'confirmation' => str_random(64),  
                'confirmed' => true  
            ]);  
        }
    }
}  
```

First I created a new instance of Faker using '$faker = Faker\Factory::create();'. 'User::truncate();' just clears the database out so we can reseed it. Next I create a loop that will create 30 new users. For any field that requires fake data just call the Faker instance we created up top and the type of data we need (e.g '$faker->userName). Faker likes to add a '.' to some usernames which doesn't play nice with my app which is why I used 'str_replace()' to get rid of those. You can even use the 'unique()' modifier to make sure usernames aren't repeated.

As you can see, it's pretty straight forward. Let's look at another example. Here I have my trip seeder.

```php  
<?php

class TripTableSeeder extends Seeder
{

    public function run()  
    {

        $faker = Faker\Factory::create();

        Trip::truncate();

        foreach(range(1,10) as $index)  
        {  
            Trip::create([  
                'user_id' => rand(1,30),  
                'location' => $faker->city . ', ' . $faker->country,  
                'from' => $from = $faker->dateTimeBetween('+1 days', '+2 years')->format('m/d/Y'),  
                'to' => $faker->dateTimeBetween($from, $from . ' +3 months')->format('m/d/Y'),  
                'note' => $faker->paragraph(1)  
            ]);  
        }  
    }  
}  
```

Same principle as before. Faker can create dates in a specific range, words, sentences and even paragraphs using the popular 'lorem ipsum' filler.

This is a great tool for projects that will be working with a lot of users and data. For more information on Faker's features, check out the <a href="https://github.com/fzaninotto/Faker" title="Faker on GitHub" target="_blank">GitHub repo</a>.

 [1]: http://www.edzynda.com/media/fake.jpg
