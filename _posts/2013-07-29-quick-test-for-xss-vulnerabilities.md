---
layout: post
title:  "Tip: Quick Test For XSS Vulnerabilities"
date:   2013-07-29 00:00:00
categories: Tips Web-Development
---
There are a lot of things to keep in mind when it comes to web development. Security should be at the top of your list. If you read up on web application security, you're probably familiar with cross-site scripting or XSS attacks. XSS attacks can cause serious damage but are really simple to prevent.

If you aren't familiar, a XSS vulnerability occurs when your application takes user data and then sends it to the web browser without proper validation. An example would be a contact form that displays a confirmation after it has been submitted. The contact form asks for your first name then displays a message like "Thanks for your inquiry Bob!"

Instead of entering 'Bob' as a first name though, an attacker can place a string of malicious javascript into the "name" field that could cause havoc on your site. Possible consequences could be defacement to inserting hidden code that would redirect future users to another malicious site controlled by the attacker.

One easy way to check whether or not you are vulnerable is to place some javascript of your own into the various fields of your web application forms.
{% highlight html5 %}
<script type="text/javascript">alert(42)</script>
{% endhighlight %}
That single line isn't malicious but it does run unintended code if your app is vulnerable. If your app is vulnerable the confirmation page will display a pop-up window with "42". A properly secured application will recognize that string as invalid and either drop any sort of tags or code or produce an error.

Using this quick test, you should be able to identify and prevent most if not all XSS attacks in your apps.
