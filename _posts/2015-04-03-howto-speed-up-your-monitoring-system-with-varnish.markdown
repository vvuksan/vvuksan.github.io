---
author: admin
comments: false
date: 2015-04-03 18:00:00+00:00
layout: post
slug: howto-speed-up-your-monitoring-system-with-varnish
title: Howto speed up your monitoring system with Varnish
---

If you use a monitoring system of any kind you are looking at lots of graphs. It also happens that
as size of your team grows you are looking at more and more graphs and often times member of your
team are looking at same graphs. In addition as you grow graphs become more complex and you may have
fairly complicated aggregated graphs with 100s of data sources which can become quite a bit of a burden
on your metrics system. This resulted in complaints about slowness of our monitoring. To speed it up
we figured we should our best bang for the buck would be to cache page fragments. Since we run a CDN
based on [Varnish](https://www.varnish-cache.org/) it was logical what we were gonna use :-).

## Assumptions 

  - Most metric systems poll on a fixed time interval e.g. 10-15 seconds. If you make a graph you can safely cache it for 10 seconds or
  longer since graph is not going to change
  - There are a number of static dashboard pages we can cache for longer since they don't change. Only dependent images change
  - Even if we don't cache or cache for really short e.g. 1-2 seconds Varnish supports [Collapsed Forward](http://wiki.squid-cache.org/Features/CollapsedForwarding)
  which will result in collapsing multiple request for same resource into one ie. if 5 clients request same resource /img/graph1.png
  at the same time varnish will send only one request to the backend then respond to all 5 clients with the same resource. This
  is ia huge win.

You can find an example Varnish configuration in this repo. This is Ganglia specific however you can adapt to suit
your needs

[Ganglia contrib repository](https://github.com/ganglia/ganglia_contrib/tree/master/varnish_web)

Key file you need is **default.vcl** which you need to put in /etc/varnish/default.vcl

## Notes

Your caching rules should be put in vcl_fetch function. For example

<pre>
if (req.url ~ "^/(ganglia2/)?$" ) {
     set beresp.ttl = 1200s;
     unset beresp.http.Cache-Control;
     unset beresp.http.Expires;
     unset beresp.http.Pragma;
     unset beresp.http.Set-Cookie;
   }
</pre>

This is a regex match that will match /ganglia2/ or / and cache it for 20 minutes (1200 seconds).
Resulting object will also be stripped of any Cache-Control, Expires, Pragma or Set-Cookie
headers since we don't want to send those to browsers. 

<pre>
   if (req.url ~ "/(ganglia2/)?graph.php") {
     set beresp.ttl = 15s;
     set beresp.http.Cache-Control = "public, max-age=10";
     unset beresp.http.Pragma;
     unset beresp.http.Expires;
     unset beresp.http.Set-Cookie;
   }
</pre>

Similar to the rule above we set cache time to 15 seconds, we unset all the headers except for Cache-Control
which we set to 10 seconds. What this will mean is that varnish will cache the object for 15 seconds however
we'll instruct the browser to cache it for 10 seconds.

You could also get creative an do things based on content type of the resulting object

<pre>
  if ( beresp.http.Content-Type ~ "image/png" ) {
     set beresp.ttl = 15s;
  }
</pre>

Have fun.