---
author: admin
comments: true
date: 2011-09-27 23:39:49+00:00
layout: post
slug: fantomtest-multiple-locations
title: Use fantomTest to test web pages from multiple locations
wordpress_id: 472
categories:
- Monitoring
---

In my previous I introduced [Testing your web pages with fantomtest](http://blog.vuksan.com/2011/08/02/testing-your-web-pages-with-fantomtest/). I have recently added ability to test the same page from multiple sites within the same interface. You simply install the copy of fantomTest on a remote site then configure your primary site to access it. For example this is a test of Google from my laptop.

[![](http://blog.vuksan.com/wp-content/uploads/2011/09/fantomtest-goog.png)](http://blog.vuksan.com/wp-content/uploads/2011/09/fantomtest-goog.png)

Looks like my network connection is really slow :-(. Changing the testing site to Croatia where I have a server I get

[![](http://blog.vuksan.com/wp-content/uploads/2011/09/fantomtest-hr.png)](http://blog.vuksan.com/wp-content/uploads/2011/09/fantomtest-hr.png)

Slightly different since Google redirects me to their localized Google site however it leads me to believe that it's my connection that is slow not Google.

Any number of  "remotes" can be added. Want it ? Get it @GitHub

[https://github.com/vvuksan/fantomtest](https://github.com/vvuksan/fantomtest)
