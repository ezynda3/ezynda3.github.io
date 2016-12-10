---
author: Ed Zynda
categories:
- Uncategorized
comments: true
date: 2013-12-27T00:00:00Z
share: true
title: My Personal Laravel Application Starter
url: /my-personal-laravel-application-starter/
---

Frameworks make it easy to hit the ground running when writing web apps. The one hurdle I run into consistently when starting any app though is creating a base user authentication system. It's pretty simple to do with most modern frameworks like Laravel but it's also just an annoying boilerplate task. This is just one of many boilerplate tasks that I've decided to eliminate by creating a basic starter app that I can pull from GitHub and modify from there.

To grab a copy of my repo you can simply run

```bash
$ git clone https://github.com/ezynda3/app-starter.git  
```

Right now it only has basic user authentication without any way to add users except manually through mysql or through a seeder file.

If you are familiar with Grunt, I've setup a Gruntfile which will monitor the assets in the 'app/assets' folder. Simply open an empty terminal an run 'grunt' inside the top level of your app directory.

I plan to improve upon this with other boilerplate items like simple 404 pages and the like in the future. Feel free to send a pull request if you have something useful to contribute.