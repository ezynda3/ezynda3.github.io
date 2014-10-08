---
title: Create A Self-destructing, Encrypted Message App In Laravel Part 2
author: Ed Zynda
layout: post
permalink: /create-a-self-destructing-encrypted-message-app-in-laravel-part-2/
dm3sb_custom_sidebar:
  - 
dm3_fwk_slideshow:
  - none
dm3_fwk_slideshow_interval:
  - 0
dsq_thread_id:
  - 1976296811
categories:
  - Frameworks
  - Laravel
  - PHP
  - Tutorials
  - Web Development
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
---
[<img src="http://www.edzynda.com/media/self-destruct-button-300x284.jpg" alt="Self Destruct Button" width="300" height="284" class="alignright size-medium wp-image-570" />][1]  
<a href="http://www.edzynda.com/create-a-self-destructing-encrypted-message-app-in-laravel-part-1/" title="Create A Self-destructing, Encrypted Message App In Laravel Part 1" target="_blank">Last week</a> we built the more complicated pieces of a simple and somewhat secure read-once, encrypted messaging app. In this part we will sketch out a simple interface using Laravel&#8217;s blade templating engine and Twitter Bootstrap.

<!--more-->

[<img src="http://www.edzynda.com/media/self-destruct-button-300x284.jpg" alt="Self Destruct Button" width="300" height="284" class="alignright size-medium wp-image-570" />][1]  
<a href="http://www.edzynda.com/create-a-self-destructing-encrypted-message-app-in-laravel-part-1/" title="Create A Self-destructing, Encrypted Message App In Laravel Part 1" target="_blank">Last week</a> we built the more complicated pieces of a simple and somewhat secure read-once, encrypted messaging app. In this part we will sketch out a simple interface using Laravel&#8217;s blade templating engine and Twitter Bootstrap.

The first thing we&#8217;ll need to do is create a default layout. This is the layout that the rest of our views will extend. Let&#8217;s save it as &#8216;app/views/layouts/default.blade.php&#8217;.

[xhtml]  
  
  
</p> 
</p> <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
  <div class="container">
    <div class="navbar-header">
      <a class="navbar-brand" href="{{ route('messages.create') }}">Burnmsg</a>
    </div></p>
  </div></p>
</div>

<div class="container">
  @yield(&#8216;content&#8217;)
</div>

<!-- /.container -->

<!-- Bootstrap core JavaScript<br />
    ================================================== -->

  
<!-- Placed at the end of the document so the pages load faster -->

  
  
  
</body>  
</html>  
[/xhtml]

Now we need to make sure that Twitter Bootstrap is accessible. Bootstrap is installed into the &#8216;vendor&#8217; folder of our project by composer. First let&#8217;s copy the Javascript libraries.

[sh]  
$ cp vendor/twitter/bootstrap/dist/js/bootstrap.min.js public/js  
[/sh]

We also need the CSS stylesheet but we are going to need to tweak some of the styles. We are going to do this using LESS.

[css]  
$ touch public/css/styles.less  
[/css]

In our newly created LESS file we will import the original stylesheet and make our tweaks as needed.

[css]  
@import &#8220;../../vendor/twitter/bootstrap/less/bootstrap&#8221;;

body {  
padding-top: 65px; /\* bring body to bottom of navbar \*/  
}

article {  
white-space: pre-wrap;  
}  
[/css]

LESS makes it easy to work with CSS but it needs to be &#8220;compiled&#8221; in order for the browser to understand it.

[sh]  
$ lessc public/css/styles.less > public/css/styles.css  
[/sh]

I use &#8216;lessc&#8217; which is a Ruby gem but you can use any LESS compiler. Google should bring up a handful of options.

Now all that is left is to create our various views which correspond to the different actions involved in our application. Let&#8217;s save these views in &#8216;app/views/messages&#8217;.

The first view &#8216;create.blade.php&#8217; will represent our message creation form. Notice that it &#8216;@extends&#8217; our default layout and renders the form within the &#8216;content&#8217; section which is referenced in the default layout.

[xhtml]  
@extends(&#8216;layouts.default&#8217;)

@section(&#8216;content&#8217;)  
{{ Form::open(['method' => 'post', 'route' => 'messages.store', 'class' => 'form']) }}

<div class="form-group">
  @if($errors->first())</p> <div class="alert alert-danger">
    {{ $errors->first() }}
  </div>
  
  <p>
    @endif<br /> {{ Form::label(&#8216;body&#8217;, &#8216;Message&#8217;) }}<br /> {{ Form::textarea(&#8216;body&#8217;, null, ['class' => 'form-control']) }}
  </p>
</div>

<div class="form-group">
  {{ Form::submit(&#8216;Submit&#8217;, ['class' => 'btn btn-primary']) }}<br /> {{ Form::close() }}
</div>

@stop  
[/xhtml]

Once the message is submitted our &#8216;store.blade.php&#8217; view will display a link for the user to copy and give to whomever they wish for later reading.

[xhtml]  
@extends(&#8216;layouts.default&#8217;)

@section(&#8216;content&#8217;)

<div class="alert alert-success">
  Your message has been saved.<br /> Here is the URL <a href="{{ route('messages.show', [$url, $key]) }}">{{ route(&#8216;messages.show&#8217;, [$url, $key]) }}</a>
</div>

@stop  
[/xhtml]

Lastly the &#8216;show.blade.php&#8217; view will display the message for the user once they decide to read it.

[xhtml]  
@extends(&#8216;layouts.default&#8217;)

@section(&#8216;content&#8217;)<article> {{{ $body }}}

  
</article> 
@stop  
[/xhtml]

Now we should have a pretty sweet messaging app that destroys your message upon reading. Keep in mind that I am no cryptography or digital forensics expert so rely on this application at your own risk. Go ahead and modify it to make it more secure if you like. Leave any suggestions you may have in the comments.

 [1]: http://www.edzynda.com/media/self-destruct-button.jpg