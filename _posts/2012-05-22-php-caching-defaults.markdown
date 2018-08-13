---
author: admin
comments: false
date: 2012-05-22 13:42:34+00:00
layout: post
slug: php-caching-defaults
title: PHP HTTP caching defaults
wordpress_id: 527
categories:
- Systems Management
---

I have recently moved this blog to be hosted on [Fastly](http://www.fastly.com/), a CDN service with bunch of great features like dynamic content caching with instant purges. Fastly utilizes HTTP headers to determine what to cache as described in the [Cache Control document](http://www.fastly.com/docs/tutorials#cache_control). While configuring my service I noticed that my Wordpress (origin server) kept returning HTTP headers like these

    
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache


I looked through the Wordpress code and couldn't see where such value was set. After some internet searches I discovered that they are set using session-cache-limiter option in php.ini. In most distributions this defaults to nocache which ends up with above headers. You can read more on the cache-limiter options here.

[http://www.php.net/manual/en/function.session-cache-limiter.php](http://www.php.net/manual/en/function.session-cache-limiter.php)

What we need is session-cache-limiter = public results in headers like these

    
    Expires: (sometime in the future, according session.cache_expire)
    Cache-Control: public, max-age=(sometime in the future, according to session.cache_expire)
    Last-Modified: (the timestamp of when the session was last saved)


e.g.

    
    Expires: Tue, 22 May 2012 16:38:33 GMT
    Cache-Control: public, max-age=10800
    Last-Modified: Tue, 04 Oct 2005 00:55:59 GMT


If you want to adjust the max-age you can set cache-expire in php.ini e.g.

    
    ; http://php.net/session.cache-expire
    session.cache_expire = 180



