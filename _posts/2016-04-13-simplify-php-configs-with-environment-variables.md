---
published: false
author: Ed Zynda
layout: post
permalink: "/simplify-php-configs-with-environment-variables/"
comments: true
categories: 
  - PHP
  - Web Development
tags: 
  - PHP
  - Environment Variables
  - Twelve Factor App
share: true
---

Modern web development has come a long way from HTML mixed with CGI scripts to well architected and complex applications that scale. Over the years, experts have come together to create design patterns and best practices that build on lessons learned. One excellent document that details best practices in the realm of web application development is "[The Twelve Factor App](http://12factor.net/)". The document's introduction does a great job of explaining it's purpose.

_In the modern era, software is commonly delivered as a service: called web apps, or software-as-a-service. The twelve-factor app is a methodology for building software-as-a-service apps that:

- Use declarative formats for setup automation, to minimize time and cost for new developers joining the project;
- Have a clean contract with the underlying operating system, offering maximum portability between execution environments;
- Are suitable for deployment on modern cloud platforms, obviating the need for servers and systems administration;
- Minimize divergence between development and production, enabling continuous deployment for maximum agility;
- And can scale up without significant changes to tooling, architecture, or development practices.

The twelve-factor methodology can be applied to apps written in any programming language, and which use any combination of backing services (database, queue, memory cache, etc)._

The document goes on to list 12 different factors that any modern web application should have. In this article, I want to cover [factor number four](http://12factor.net/backing-services).

Basically, factor four discusses the idea of backing services or resources. Most web applications utilize at least one external resource. A database is probably the most common. A modern web application shouldn't really care too much about the details of these resources except how to connect to it. These resources should be interchangeable without the need for a change in your source code.

One of the first pieces that need to be in place for this is having a flexible configuration system. In PHP you can store configuration in a handful of ways. Probably the most common and easiest is to just store it in code itself. You can orginaize it and keep it somewhat manageable by keeping PHP files with config settings separate. The only problem is that you need to actually edit the source whenever you want to make a configuration change.

There is a better way. 




