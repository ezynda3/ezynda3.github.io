---
author: Ed Zynda
categories:
- Frameworks
- Laravel
- PHP
- Tutorials
- Web Development
comments: true
date: 2013-11-18T00:00:00Z
share: true
tags:
- Blade
- Bootstrap
- CSS
- Encryption
- javascript
- laravel
- LESS
- PHP
- self-destruct
- Twitter
title: Create A Self-destructing, Encrypted Message App In Laravel Part 2
url: /create-a-self-destructing-encrypted-message-app-in-laravel-part-2/
---

The first thing we'll need to do is create a default layout. This is the layout that the rest of our views will extend. Let's save it as 'app/views/layouts/default.blade.php'.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Burnmsg - Self-desctructing, encrypted messages</title>

    <!-- Bootstrap core CSS -->
    <link href="{{ asset('css/styles.css') }}" rel="stylesheet">

    <!-- HTML5 shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
      <script src="https://oss.maxcdn.com/libs/respond.js/1.3.0/respond.min.js"></script>
    <![endif]-->
  </head>

  <body>

    <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="{{ route('messages.create') }}">Burnmsg</a>
        </div>
       </div>
    </div>

    <div class="container">
        @yield('content')
    </div><!-- /.container -->


    <!-- Bootstrap core JavaScript
    ================================================== -->
    <!-- Placed at the end of the document so the pages load faster -->
    <script src="https://code.jquery.com/jquery-1.10.2.min.js"></script>
    <script src="{{ asset('js/bootstrap.min.js') }}></script>
  </body>
</html>
```

Now we need to make sure that Twitter Bootstrap is accessible. Bootstrap is installed into the 'vendor' folder of our project by composer. First let's copy the Javascript libraries.

```bash  
$ cp vendor/twitter/bootstrap/dist/js/bootstrap.min.js public/js  
```

We also need the CSS stylesheet but we are going to need to tweak some of the styles. We are going to do this using LESS.

```bash  
$ touch public/css/styles.less  
```

In our newly created LESS file we will import the original stylesheet and make our tweaks as needed.

```css  
@import "../../vendor/twitter/bootstrap/less/bootstrap";

body {
    padding-top: 65px; /* bring body to bottom of navbar */
}

article {
    white-space: pre-wrap;
}
```

LESS makes it easy to work with CSS but it needs to be "compiled" in order for the browser to understand it.

```bash
$ lessc public/css/styles.less > public/css/styles.css  
```

I use 'lessc' which is a Ruby gem but you can use any LESS compiler. Google should bring up a handful of options.

Now all that is left is to create our various views which correspond to the different actions involved in our application. Let's save these views in 'app/views/messages'.

The first view 'create.blade.php' will represent our message creation form. Notice that it '@extends' our default layout and renders the form within the 'content' section which is referenced in the default layout.

{% raw %}
```html  
    @extends('layouts.default')

    @section('content')
    {{ Form::open(['method' => 'post', 'route' => 'messages.store', 'class' => 'form']) }}
    <div class="form-group">
        @if($errors->first())
            <div class="alert alert-danger">
                {{ $errors->first() }}
            </div>
        @endif
        {{ Form::label('body', 'Message') }}
        {{ Form::textarea('body', null, ['class' => 'form-control']) }}
    </div>
    <div class="form-group">
        {{ Form::submit('Submit', ['class' => 'btn btn-primary']) }}
        {{ Form::close() }}
    </div>
    @stop
```
{% endraw %}

Once the message is submitted our 'store.blade.php' view will display a link for the user to copy and give to whomever they wish for later reading.

{% raw %}
```html
    @extends('layouts.default')

    @section('content')
    <div class="alert alert-success">
        Your message has been saved.
        Here is the URL <a href="{{ route('messages.show', [$url, $key]) }}">{{ route('messages.show', [$url, $key]) }}</a>
    </div>
    @stop
```
{% endraw %}

Lastly the 'show.blade.php' view will display the message for the user once they decide to read it.

{% raw %}
```html
    @extends('layouts.default')

    @section('content')
    <article>
    {{{ $body }}}
    </article>
    @stop
```
{% endraw %}

Now we should have a pretty sweet messaging app that destroys your message upon reading. Keep in mind that I am no cryptography or digital forensics expert so rely on this application at your own risk. Go ahead and modify it to make it more secure if you like. Leave any suggestions you may have in the comments.

 [1]: http://www.edzynda.com/media/self-destruct-button.jpg
