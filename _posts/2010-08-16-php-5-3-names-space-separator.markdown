---
author: admin
comments: true
date: 2010-08-16 19:03:30+00:00
layout: post
slug: php-5-3-names-space-separator
title: PHP 5.3 name spaces separator
wordpress_id: 334
tags:
- PHP
---

I am posting this to help others that may encounter a similar problem.

I have been doing some PHP development recently using [Predis](http://github.com/nrk/predis/), a PHP [Redis](http://code.google.com/p/redis/) library. While instantiating the Redis\Client object I get

    
    Warning: Unexpected character in input:  '\' (ASCII=92) state=1 in .....


Problem was explained in this issue

[http://github.com/nrk/predis/issues/closed#issue/11](http://github.com/nrk/predis/issues/closed#issue/11)


If you are still running on PHP 5.2 you should use the backported  version of Predis and not the mainline library which targets only PHP  >= 5.3 (the _**backslash is the namespace separator**_ in PHP 5.3).


More discussion of this change can be found here.

[http://giorgiosironi.blogspot.com/2009/09/introspection-of-php-namespaces.html](http://giorgiosironi.blogspot.com/2009/09/introspection-of-php-namespaces.html)
