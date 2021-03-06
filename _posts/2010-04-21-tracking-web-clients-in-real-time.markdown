---
author: admin
comments: true
date: 2010-04-21 01:35:42+00:00
layout: post
slug: tracking-web-clients-in-real-time
title: Tracking web clients in real time
wordpress_id: 170
categories:
- Monitoring
- Systems Management
tags:
- Memcached
---

Most recently I have been working on being able to more quickly identify abusers of our service ie. spammers, crawlers etc. We already have a process that rotates web logs on all web servers hourly then processes them extracting per IP access info. On occasion abusers get quite aggressive and cause some of our alarms to go off by causing excessive number of log errors etc. Trouble is that due to logs being processed on the hour there is a window of time where we may spend extra time trying to track down the cause of log errors. I figured it would help if the IP tracker was real-time. Luckily we have already been using a package called Ganglia Logtailer

[http://bitbucket.org/maplebed/ganglia-logtailer/](http://bitbucket.org/maplebed/ganglia-logtailer/)

which processes our web logs every minute and publishes metrics such as number of HTTP 200/300/400/500 hits, average and 90th percentile response time. All I had to do was send the IP data to a storage engine of my choice. Initially I thought I could use mySQL however decided against it due to following reasons



	
  1. Currently we can get up to 2500 hits/sec so processing them on the minute would result in roughly 150k inserts which mySQL may have some trouble processing in short amount of time.

	
  2. I don't need this data after couple hours.


I looked at Redis which has some interesting features around sets however I decided to use memcached since we were already using it and if I ever wanted to use a more persistent storage engine I could replace it with memcachedb or Tokyo Cabinet with no changes to the code.

**Implementation**

Implementation consists of two pieces

1. Modified Ganglia Logtailer class that inserts data into memcached. You can find a VarnishMemcacheLogtailer class on the Bit Bucker logtailer site which implements this. All you have to do is modify the location of the memcached server (set to localhost). Current implementation aggregates data per hour ie. all the numbers are hourly numbers. It would be trivial to do it for 10 minute or 1 minute periods.

2. Client application that displays data from memcached. I wrote a PHP interface that shows top 20 IPs from the web servers that can be downloaded from here
[](http://bitbucket.org/vvuksan/realtime-iptracker)

[http://bitbucket.org/vvuksan/realtime-iptracker](http://bitbucket.org/vvuksan/realtime-iptracker)

Tracker looks something like this

[![](http://blog.vuksan.com/wp-content/uploads/2010/04/iptracker.png)](http://blog.vuksan.com/wp-content/uploads/2010/04/iptracker.png)**Update: **I do realize Splunk would be great for this kind of a purpose. Trouble is that for the amount of logs we create we'd have to get a really large Splunk license and those are quite expensive.
