---
author: admin
comments: true
date: 2011-04-19 19:59:49+00:00
layout: post
slug: use-your-trending-data-for-alerting
title: Use your trending data for alerting
wordpress_id: 440
categories:
- Monitoring
tags:
- Nagios
---

This post will deal with helping you use the data you already have to do alerting. It is most helpful for people running Nagios or it's variants such as Icinga, Netreo etc. It could likely be used with other decoupled alerting systems (not Zabbix or Zenoss though since they do their own trending).

Recently I came to a realization that lots of sysadmins are unaware that they could easily use trending data they already capture with systems such as Ganglia, Graphite, Collectd, Munin etc. to do alerting. Standard way of doing health checks of remote nodes in Nagios is to install the [Nagios Remote Plugin Executor aka. NRPE](http://exchange.nagios.org/directory/Addons/Monitoring-Agents/NRPE-%252D-Nagios-Remote-Plugin-Executor/details) which allows you to execute Nagios plugins on remote nodes and pipe output to the Nagios server. NRPE does the job however has three major disadvantages



	
  1. It is another daemon that needs to run on the remote host possibly introducing security concerns

	
  2. Depending on the load of the machine can be slow thus bogging down the Nagios server

	
  3. Last and most important is that commonly it's used to alert on common metrics such as disk, load, CPU, swap which you should be trending anyways.


Instead what you ought to be doing is use trending data for alerting. I can think of at least 4 reasons to do so

	
  1. You may already be collecting pertinent data ie. system load, swap, CPU utilization

	
  2. If you are alerting on a particular metric you should likely be trending it

	
  3. It's fast

	
  4. Allows you to do more sophisticated checks easily ie. alert me if more than 5 hosts have a load greater than 5 etc.


Years ago I used Ganglia Web PHP code to write my own generic [Nagios Ganglia plugin](http://vuksan.com/linux/nagios_scripts.html#check_ganglia_metrics). This has served me well. Most recently [Michael Conigliaro](Michael Conigliaro) rewrote the script in Python making it more versatile and more powerful. You can download it from here

[https://github.com/ganglia/ganglia_contrib/tree/master/nagios](https://github.com/ganglia/ganglia_contrib/tree/master/nagios)

In a nutshell what it does is download the whole metrics tree ie. list of all hosts with their associated metrics. Caches it for a configurable amount of time then uses [NagAconda](http://packages.python.org/NagAconda/plugin.html) to support all the threshold reporting as defined in [Nagios developer guidelines](http://nagiosplug.sourceforge.net/developer-guidelines.html#THRESHOLDFORMAT).

Another alternative if you have a very large site is Ganglios which was opensourced by guys at Linden Lab. Their problem is/was that they have thousands of hosts and downloading the whole metrics tree takes ~15 seconds so they have separated the logic that downloads the metric tree and one that does alerting. You can download Ganglios from

[https://bitbucket.org/maplebed/ganglios](https://bitbucket.org/maplebed/ganglios)

This can easily be adapted to work with your trending system of choice.
