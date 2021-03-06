---
author: admin
comments: true
date: 2010-01-20 14:34:08+00:00
layout: post
slug: cool-dns-hacks-you-cant-use-for-fail-over
title: Cool DNS tricks you can't use for fail-overs
wordpress_id: 69
categories:
- Networking
---

At a previous job for availability and business continuity reasons we set up a geographically redundant data center because even the best data centers will have outages. No matter what a vendor tells you processes are never followed fully. You can also have a major disaster with critical pieces of your hardware that may cripple or disable your whole infrastructure ie. switch goes crazy etc.

Service we provided was critical so highest availability was imperative. Management wanted an active-active set up ie. use both data centers in a load-balanced fashion however that would have entailed extensive application rewrite due to the nature of our application and the level of database transactions involved. Thus we settled on a hot-cold configuration where we would have an active site that was serving customers and a cold site that was kept up to date via replication. In case of trouble (as determined by ops) we would fail-over our hot site to the cold site. This is fairly straight forward except for the part where you are actually failing things over ie. your hot site is down, you break off replication, change DNS entries, start up all the necessary services however due to [DNS caching](http://www.bretpiatt.com/blog/2009/10/03/availability-is-a-fundamental-design-concept/#jsid-1254638526-674) some of your customers are still pointing to your "dead" site. Depending on your browser this could be 30 minutes+. Did I mention this service was critical ?

We went through the list of possible options on how to resolve this

1. Use an outside party load balancer(s) ie. an off-site load balancer(s) that would proxy traffic to the site that was live. This seemed like a plausible idea however we didn't like the fact we were introducing yet another failure point and adding latency due to extra round-trip.

2. Changed DNS TTL to 2 minutes however that was also insufficient due to different browsers behavior. For example IE 6 (perhaps even higher) will cache DNS entries for 30 minutes

[http://support.microsoft.com/kb/263558](http://support.microsoft.com/kb/263558)

3. Use [round-robin DNS](http://en.wikipedia.org/wiki/Round_robin_DNS) aka. multiple DNS A records with a "twist"

What we did there is put both of our data center's IPs into the A record for our site ie.

    
    www.domain.com   IN A 1.2.3.4
    www.domain.com   IN A 9.8.7.6


What happens with most browsers is that they will attempt the first IP and if they get a connection refused they will try the next (and next if you have more than 2). This actually works quite well e.g. even if the browser was getting requests from 1.2.3.4 if 1.2.3.4 all of the sudden goes down it will in sub-second time fail-over to 9.8.7.6. The "twist" we added was that we only answered on the active colo IP and returned connection closed on the inactive. If we needed to failover we'd just swap one colo and deactivate the other. Quick failovers here we come :-).

This all worked great for some time until we started receiving isolated reports that people weren't able to access our site. Investigating the issue further we discovered that all of the people having connectivity issues were behind a transparent HTTP proxy. In this particular case the transparent proxy would not return connection refused but "page not found" or something similar neutralizing our clever hack :-(.

Obviously if you audience is different and you know your users don't use proxies you could use this approach however this doomed it for us.
