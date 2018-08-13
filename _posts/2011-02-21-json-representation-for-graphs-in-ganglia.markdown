---
author: admin
comments: true
date: 2011-02-21 02:38:46+00:00
layout: post
slug: json-representation-for-graphs-in-ganglia
title: JSON representation for graphs in Ganglia
wordpress_id: 431
categories:
- Monitoring
---

Recently thanks to work done by Alex Dean aka. [@mostlyalex](https://twitter.com/mostlyalex) Ganglia UI supports defining custom graphs using JSON. Prior to this only way to create custom graphs was by writing custom PHP code. This has two major problems ie. lots of people are not comfortable writing or modifying PHP code and second you have to target a particular graphing engine e.g. rrdtool. As I have written in the past we are gonna be supporting both rrdtool and graphite for graphing so having a common way to describe graphs has been one of our goals.

To describe a custom graph you would create a JSON file similar to this one

    
    {
     "report_name" : "network_report",
     "report_type" : "standard",
     "title" : "Network Report",
     "vertical_label" : "Bytes/sec",
     "series" : [
     { "metric": "bytes_in", "color": "33cc33", "label": "In", "line_width": "2", "type": "line" },
     { "metric": "bytes_out", "color": "5555cc", "label": "Out", "line_width": "2", "type": "line" }
     ]
    }


This will create a line graph with bytes_in and bytes_out metrics. Since hostname and cluster are not specified it is assumed that we want metrics for the current host we are viewing. You could however specify a particular host and metric you want to graph by adding hostname and cluster attributes to series ie.

    
    {
     "report_name" : "our_load_report",
     "report_type" : "standard",
     "title" : "Load Report vs. Database Load",
     "vertical_label" : "Loads",
     "series" : [
     { "metric": "load_one", "color": "3333bb", "label": "Load 1", "line_width": "2", "type": "line" },
     { "hostname": "db1.domain.com", "clustername": "Databases", "metric": "load_one", "color": "44ddbb", "label": "DB1 Load 1", "line_width": "2", "type": "line" },
     ]
    }


To use the reports all you have to do is put the report in the $GANGLIA_WEB_ROOT/graph.d directory. Name them something_report.json and it will be available for any host in the cluster. There is one important thing to note. By default graphing function will look for PHP definitions for graphs as those in theory provide more power and flexibility and if those are not available use JSON definition.


### Types of graphs


Currently both line and stacked graphs are supported. Look in graph.d/ directory for additional examples.


### Future


I am particularly excited about this feature as it allows us to define [aggregate graphs](http://blog.vuksan.com/2010/06/05/beauty-of-aggregate-line-graphs/) easily. There is even an alpha implementation of functionality which would allow you to specify a metric and a regex host entry and you would end up with an aggregate graph :-).


### Download location


Latest version of the UI can be downloaded either from [Ganglia Monitor Web 2.0 SVN branch](http://ganglia.svn.sourceforge.net/svnroot/ganglia/branches/monitor-web-2.0/) or you can get it on [Github](https://github.com/vvuksan/ganglia-misc).
