---
author: admin
comments: false
date: 2012-09-01 14:51:00+00:00
layout: post
slug: my-monitoring-setup
title: My monitoring setup
wordpress_id: 566
categories:
- Monitoring
---

On Twitter [Grig Gheorghiu](https://twitter.com/griggheo) posed a number of questions about monitoring tools and [what he wants in a monitoring tool](http://agiletesting.blogspot.com/2012/09/what-i-want-in-monitoring-tool.html). This is my attempt at describing what my setup looks like or has looked like in the past.

**1. Metrics acquisition / performance trending**

I use Ganglia to collect all my metrics including string metrics. Base installation for Ganglia Gmond will give you over 100 metrics. There are a number of Python modules that are disabled by default like mySQL, Redis that you can easily enable and get more. If you need even more you can check out these to Github repositories.



	
  * [Gmond Python Modules](https://github.com/ganglia/gmond_python_modules/)

	
  * [Gmetric](https://github.com/ganglia/gmetric/) scripts


Don't worry about sending too many metrics. I have hosts that send in excess of 1100 metrics per host. Ganglia can handle it so don't be shy :-). Also when I say all metrics go into Ganglia I mean EVERYTHING. If I want to alert on it it will be in Ganglia so I have things like these

	
  * NTP time offset

	
  * What version is particular key piece of software on e.g. deploy ID 123af58

	
  * Memory utilization/CPU utilization for key daemon processes

	
  * [Number of failed disks in a RAID array](http://blog.vuksan.com/2012/09/28/monitoring-health-of-delllsi-raid-arrays-with-ganglia/)

	
  * Application uptime

	
  * Etc.


**2. Alerting**

I use Nagios or Icinga for alerting. I don't really use any Nagios plugins as all the checks are driven by data coming out of Ganglia. I have written a post in the past about [why you should use your trending data for alerting](http://blog.vuksan.com/2011/04/19/use-your-trending-data-for-alerting/) which you can read for some background. About a year ago [Ganglia/Nagios integration](https://github.com/ganglia/ganglia-web/wiki/Nagios-Integration) has been added to Ganglia Web which makes a number of things much easier so for example I have



	
  * A single check that checks all hosts in the cluster for failed disk in a RAID array

	
  * A single check that checks whether time is within certain offset on all hosts ie. to make sure NTP is actually running

	
  * A single check that makes sure version of deployed code is the same everywhere

	
  * A single check for all file systems on a local system with each file system having their own thresholds

	
  * A single check for elevated rates of TCP errors - useful to get a quick idea if things are globally slow, affecting a certain set of hosts in a geographic area or an individual host


Beauty in having all of the metric data in Ganglia is that you can also get creative by writing custom checks that have your own user specified logic e.g.

	
  * Alert me only if 20% of my data nodes are down e.g. in architectures where you can withstand few nodes failing


In addition I recommend adding as much [context to alerts](http://blog.vuksan.com/2012/03/29/adding-context-to-your-alerts/) as possible so that you get as much as information in alert as possible.

I also heavily utilize something I wrote called the alerting controller

[https://github.com/vvuksan/alerting-controller](https://github.com/vvuksan/alerting-controller)

Which allows you easily enable/disable/schedule downtime for services in Nagios e.g. disable process alive check for configuration servlet on all hosts while we are doing an upgrade etc. In addition I have a tab with Naglite opened up most of the time to check on any outstanding alerts.

[https://github.com/saz/Naglite3](https://github.com/saz/Naglite3)

**3. Notifications**

Beyond just alerting I am always interested to see what is happening with the infrastructure so we can act proactively. For that purpose I use IRC and a modified version of [NagiosBot ](https://github.com/cluenet/cluemon/blob/master/nagios-bin/nagiosbot.py)which sends echoes following things to the channel



	
  * Nagios alerts (same script that adds context to alerts above) - helpful for quick team coordination

	
  * Dynect DNS zone changes - those may be "invisible" so good idea to track

	
  * Zendesk tickets - anyone can handle support requests

	
  * Twitter mentions of a particular keyword

	
  * Application configuration changes e.g. new version of the code deployed or in progress

	
  * Severe Application errors - short summary of an error and which node it occured on


**Closing**

This is by no means an exhaustive list and this may not be the best way to do things but it does work for me.
