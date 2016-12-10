---
author: Ed Zynda
categories:
- PHP
- Web Development
comments: true
date: 2016-04-13T00:00:00Z
share: true
tags:
- PHP
- Environment Variables
- Twelve Factor App
title: Simplify PHP Configs With Environment Variables
url: /simplify-php-configs-with-environment-variables/
---

Laravel 5 and above icludes a cool feature to help simplify configuration and deployment to different environments. Before you had to create separate directories within 'app/config' to separate configuration based on the environment. You would then use varying methods to tell Laravel which environment it was in a bootstrap file. This can get pretty messy real quick epecially if you are adding third party libraries that may require some special configuration options.

In Laravel 5 and above you just create a file name '.env' and place your configuration in environment variables. The file would look something like this.

```sh
DB_NAME=mydatabse
FOOBAR=baz
SOME_API_KEY=1234abcef
```

You would create this file and make sure to add '.env' to '.gitignore' in order to keep sensitive data out of your code repository. When you deploy to your different environments you can then just include a '.env' file that's appropriate for the environment.

Laravel 4.2 and below doesn't have this but fortunately, with a little bit of tweaking you can add it.

First we need to include a helpful package that reads '.env' files and then loads them for PHP to access.

```sh
composer require vlucas/phpdotenv
```
If you're using Laravel 4.2 you next need to edit 'bootstrap/start.php'. If you're using another framework or none at all you just need to add the following code to wherever you bootstrap your application.

```php
<?php
if (is_file(__DIR__ . '/../.env')) {
	Dotenv::load(__DIR__ . '/../');
}
```

This will check to see that the file exists and if so, load the data into PHP's $_ENV superglobal array.

Next we'll need a helper function to grab environment variables or use an acceptable default. If you're using Laravel, place this in 'app/helpers.php' if not you can place it wherever you keep helper functions.

```php
<?php
if ( ! function_exists('env'))
{
    /**
     * Gets the value of an environment variable. Supports boolean, empty and null.
     *
     * @param  string  $key
     * @param  mixed   $default
     * @return mixed
     */
    function env($key, $default = null)
    {
        $value = getenv($key);

        if ($value === false) return value($default);

        switch (strtolower($value))
        {
            case 'true':
            case '(true)':
                return true;

            case 'false':
            case '(false)':
                return false;

            case 'null':
            case '(null)':
                return null;

            case 'empty':
            case '(empty)':
                return '';
        }

        return $value;
    }
}
```

Now you should be able to use .env files an consolidate your configuration data. For example if you need a config file like 'app/config/sendgrid.php' to handle sending mail with SendGrid you can make it look something like this.

```php
<?php
return [
    'api_key' => env('SENDGRID_API_KEY')
];
```

This will greatly simplify your deployments and help avoid configuration comlexity in your older Laravel or other non-Laravel applications.
