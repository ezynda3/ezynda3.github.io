---
author: Ed Zynda
categories:
- PHP
- Web Development
comments: true
date: 2013-11-08T00:00:00Z
share: true
tags:
- FIG
- interoperability
- PHP
- PSR
- renaissance
- standards
title: 'The PHP Renaissance: FIG and PSRs'
url: /the-php-renaissance-fig-and-psrs/
---

If you worked with PHP in the early days or even if you are a newcomer, you are probably familiar with how messy and painful working with another developer&#8217;s code can be. PHP has a low barrier to entry which is great for getting things done. At the same time though, it creates a culture of lazy developers. Even those developers with years of experience have coding styles that are hard to wade through. Like some people&#8217;s handwriting, it only makes sense to them. This is fine if they don&#8217;t plan on sharing the code or expect someone else to have to maintain it later. In reality though, a lot of code these days is opensource or will eventually become someone else&#8217;s responsibility to maintain. Some top PHP developers have realized this and come together to try and shape the PHP community for the better.

A lot of large PHP projects are built on frameworks because why re-invent the wheel. There are a handful of popular frameworks in active development today and they all have their pros and cons. It would be nice for a developer if he/she could use one framework for a project one week and start another project using a different framework the next week with minimal time spent learning the intricacies of the new framework. It would also be nice if the source code to these frameworks could be readable by anyone with a basic knowledge of PHP.

The <a href="http://php-fig.org" title="PHP Framework Interop Group" target="_blank">PHP Framework Interop Group (FIG)</a> is trying to accomplish exactly this.

> The idea behind the group is for project representatives to talk about the commonalities between our projects and find ways we can work together. Our main audience is each other, but we’re very aware that the rest of the PHP community is watching. If other folks want to adopt what we’re doing they are welcome to do so, but that is not the aim.

The group takes the input from active community members and submits a Proposed Standards Recommendation or PSR. PSRs are then voted on and approved. So far there have been 4 PSRs approved by the group.

*   **PSR-0 &#8211; Autoloading Standard**
    Provides a standard for namespacing your classes and the directory structure they reside in. This allows for interoperability between frameworks.

*   **PSR-1 &#8211; Basic Coding Standard**
    This section of the standard comprises what should be considered the standard coding elements that are required to ensure a high level of technical interoperability between shared PHP code.

*   **PSR-2 &#8211; Coding Style Guide**
    The intent of this guide is to reduce cognitive friction when scanning code from different authors. It does so by enumerating a shared set of rules and expectations about how to format PHP code.

*   **PSR-3 &#8211; Logger Interface**
    This defines a LoggerInterface that third party libraries should implement in order to store logs in a universal way. </ul>
    Keep in mind that these standards are in no way, hard and fast rules for coding PHP. As long as your PHP has proper syntax, the PHP engine will properly interpret it. There is no guarantee that another human being will bother to though. If you are a contributor to open source projects or just want to develop cleaner or more readable code, I recommend you go over some of these standards and try to adhere to them yourself.

    Some say that with the rise of languages like Python and Ruby, that PHP is on it&#8217;s way out. I disagree. PHP is stronger than ever now. There is a sort of Renaissance happening and the work done by FIG is helping to push the language forward.

    You can read about the various standards in depth at <a href="http://php-fig.org" title="PHP Framework Interop Group" target="_blank">php-fig.org</a>.

 [1]: http://www.edzynda.com/media/rennaissance.jpg
