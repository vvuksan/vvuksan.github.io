---
author: admin
comments: true
date: 2012-04-06 23:12:05+00:00
layout: post
slug: compute-a-15-minute-average-of-a-metric-easily-with-ganglia
title: Compute a 15 minute average of a metric easily with Ganglia
wordpress_id: 516
---

This is a quick way to extract a 15 minute average of a metric in Ganglia. It utilizes Ganglia's CSV export function to get the values then uses awk to actually compute the average.

First of all find a metric graph you want to calculate average from. Right click over the image and copy the image location. Then append &csv=1 to the URL and UNIX time stamp from 15 minutes ago and put that as the &cs= argument. This is a simple shell script that illustrates it

    
    MIN15AGO=`date --date="15 minutes ago" "+%s" ; 
    curl --silent "http://ganglia.domain.com/ganglia/graph.php?c=NetApp&h=host1&v=&m=netapp_cpuutil&<span style="color: #ff0000;">cs=$MIN15AGO&csv=1</span>" | \
       awk -F, '{sum+=$2} END { print "Average = ",sum/NR}'
    
    
    
