---
author: Ed Zynda
categories:
- PHP
- Tutorials
- Web Development
- Command Line
comments: true
date: 2014-11-03T00:00:00Z
share: true
tags:
- Symfony
- CLI
- Composer
- PHP
- Marvel
- API
title: Create A Simple PHP Command Line Tool
url: /create-a-simple-php-command-line-tool/
---

PHP is no doubt a great fit for building web applications but it also has the ability to create powerful command line applications. PHP has had this ability for a long time but recently there has been some excellent work done in order to improve the quality of PHP command line tools. The [Symfony\Console](http://symfony.com/doc/current/components/console/introduction.html) component makes it incredibly easy to create a command line application that accepts different arguments and options. Let's see how easy it can be.

I've decided to do something a little different then the standard "do stuff with Twitter" app that seems to be popular these days. Instead let's do stuff with Marvel. More specifically let's use data from the [Marvel Developer's API](http://developer.marvel.com/) to make a command line app that searches for and displays the description of different characters from the Marvel universe. We'll use an incomplete but usable PHP library called [Marvel-PHP](https://github.com/caseysoftware/marvel-php) to help us interact with the API. To get started, create a new directory for your project and call it whatever you like.

**Setup the Dependencies**

First we need to make sure we pull in the Symfony\Console component and the Marvel-PHP library with composer. Create a 'composer.json' file at the root of your project that looks like this then run 'composer install' from the command line.

```json
{
    "require": {
        "symfony/console": "~2.5",
        "caseysoftware/marvel-helper": "dev-master"
    },
    "autoload": {
        "psr-4": {
            "Stark\\": "app"
        }
    }
}

```

We pulled in the our dependencies but we've also created a namespace for our app called 'Stark' that lives in the 'app' directory. That is where the meat of our command line app will live. Go ahead and create that.

**Bootstrap the Application**

In a normal modern web application, we would most likely have a front controller, usually index.php, as our entry point to the application. This would take care of the routing and bootstrapping of the rest of the application. A command line application isn't much different. Let's create shell script to bootstrap the rest of the app. First, as you'll see in the code below, you will need to acquire credentials from Marvel to access the API. You can grab a public and private key pair [here](https://developer.marvel.com/account). Once you have that, you're ready to continue. Create a file called 'marvel' at the top level of your app and paste the following code.

```php
#!/usr/bin/env php
<?php
require_once 'vendor/autoload.php';

use Stark\Console\Command\SearchCharacterCommand;
use Symfony\Component\Console\Application;

// API Keys
$public_key = '<YOUR_PUBLIC_KEY>';
$private_key = '<YOUR_PRIVATE_KEY>';

$client = new Services_Marvel($public_key, $private_key);

// Bootstrap
$application = new Application();
$application->add(new SearchCharacterCommand($client));
$application->run();
```
The first line tells the shell or command line interface (bash, sh, zsh, etc...) to use the PHP interpreter to execute the rest of the script. Then we require composer's autoloader which gives us access to our dependencies and the 'Stark' namespace we created.

Symfony command line applications are instantiated from the 'Symfony\Component\Console\Application' class and can have a number of commands associated with them. We will create a search command called 'Stark\Console\Command\SearchCharacterCommand'.

Fill out the placeholders for the API keys with the keys you acquired from Marvel. Next we pass those into a new instance of the Marvel-PHP client we included.

Finally we add a new instance of the 'SearchCharacterCommand' to our application and call the 'run()' method. Symfony takes care of the rest.

**Define a Command**

Now we need define the actual command that should be executed when we run this from the command line. Symfony\Console applications can have multiple commands that can have various arguments and options. Let's see what that would look like.

```php
<?php namespace Stark\Console\Command;

use Services_Marvel;
use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Formatter\OutputFormatterStyle;

class SearchCharacterCommand extends Command
{
    /**
     * @var Services_Marvel
     */
    private $client;

    /**
     * Constructor
     * @param Services_Marvel $client
     */
    public function __construct(Services_Marvel $client)
    {
        parent::__construct();
        $this->client = $client;
    }

    /**
     * Configure the command
     */
    protected function configure()
    {
        $this
            ->setName('search:character')
            ->setDescription('Search for a Marvel character')
            ->addArgument(
                'name',
                InputArgument::REQUIRED,
                'Who do you want to search for?'
            )
            ->addOption(
                'titles',
                null,
                InputOption::VALUE_NONE,
                'Display associated titles.'
            );
    }

    /**
     * Execute the command
     * @param InputInterface $input
     * @param OutputInterface $output
     */
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $style = new OutputFormatterStyle('red', 'yellow', array('bold', 'blink'));
        $output->getFormatter()->setStyle('attribution', $style);

        $name = $input->getArgument('name');

        $text = '';

        $characters = $this->client->characters->index(1, 25, ['name' => $name]);

        if (! $characters) {
            $text = '<error>No results found for ' . $name . '.</error>' .PHP_EOL;
        }

        foreach ($characters as $character) {
            $text .= $character->name . PHP_EOL . PHP_EOL
                . '<info>Description:</info>' . PHP_EOL . PHP_EOL
                . $character->description . PHP_EOL . PHP_EOL;

            // --title
            if ($input->getOption('titles')) {
                $text .= '<info>Titles:</info>' . PHP_EOL . PHP_EOL
                    . $this->getSeries($character) . PHP_EOL;
            }

            $text .= '<attribution>Data provided by Marvel. © 2014 Marvel</attribution>';
        }

        $output->writeln($text);
    }

    /**
     * Get the comic book titles for the characters
     * @param $character
     * @return string
     */
    private function getSeries($character)
    {
        $titles = '';
        foreach ($character->series->items as $series) {
            $titles .= $series->name . PHP_EOL;
        }

        return $titles;
    }
}
```

All commands extend 'Symfony\Component\Console\Command\Command'. The 'configure()' method is used to tell Symfony how to invoke the command. In this case it's 

```bash
$ ./marvel search:character <name>
``` 

We can create other commands if we want to but we'll stick with one for now. We also define arguments and options to accept. In this case the 'name' argument is required but the 'titles' option is not. So to run a character search you would need to do something like.

```bash
$ ./marvel search:character Thor
```

To display the associated comic book titles, add the 'titles' option like this.

```bash
$ ./marvel search:character --titles Thor
```

The output looks something like this.

![Output](/images/marvel-search-output.png)

The 'execute()' method tells Symfony what logic to run when our command is invoked. I won't get too much into how the API client works but you can check out some of the examples in the library's [Github repo](https://github.com/caseysoftware/marvel-php).

This should give you a taste of what kind of cool command line tools you can create. For more information and to learn what else the Symfony\Console component can do, check out the [documentation](http://symfony.com/doc/current/components/console/introduction.html).


