---
author: admin
comments: true
date: 2010-12-14 22:04:57+00:00
layout: post
slug: misconceptions-about-rrd-storage
title: Misconceptions about RRD storage
wordpress_id: 424
categories:
- Monitoring
- Systems Management
tags:
- ganglia rrd monitoring
---

I want to address the misconceptions about RRD (Round-Robin Database) that seem to crop up often even among seasoned sysadmins. Complaints can be summarized with these two points



	
  * RRD doesn't offer high resolution ie. after about an hour it's all averages and I want to knows what was the metric value last year at this hour and minute

	
  * Data drops off/is destroyed after a year - I want to keep my data forever, disk is cheap etc.


Those are valid points however none of them are the fault of RRD. RRD is a circular buffer so in order to be able to write into it you have to precreate it (otherwise it wouldn't be a circular buffer :-)). Obviously more data points you store bigger the RRD file will be. To illustrate the point [Ganglia Monitoring](http://ganglia.info/) uses following defaults to create RRDs

RRAs "RRA:AVERAGE:0.5:1:244" "RRA:AVERAGE:0.5:24:244" "RRA:AVERAGE:0.5:168:244" "RRA:AVERAGE:0.5:672:244" "RRA:AVERAGE:0.5:5760:374"

This will create multiple circular buffers within the same RRD database file. In order to make sense out of this you need to know what the polling interval is ie. how often do you write into RRDs. In Ganglia's case the default is 15 seconds so



	
  * "RRA:AVERAGE:0.5:1:244" says write actual values (:1:) for every polling interval. Save last 244 of those so in our case we'll have 61 minutes worth of actual data points. Since it's a circular buffer data older than 61 minutes will be "dropped"

	
  * "RRA:AVERAGE:0.5:24:244" says average 24 values (:24:), 24 * 15 seconds = 360 seconds = 6 minutes. 244 of those times 6 is a whole day

	
  * You can do the next two :-)

	
  * Last one "RRA:AVERAGE:0.5:5760:374" says average whole day (5760 * 15 seconds = 1440 minutes = 1 day) worth of values and store it in 374 points ie. little more than a year


When graphing RRDtool is smart enough to use the buffer which gives you the most data points. To store all this data RRD file will use about 12kBytes. Thus if you want higher resolution you will need to change the definition e.g. you could do this

"RRA:AVERAGE:0.5:1:2137440"

which will give you one year worth of data points with no averaging with 15 second interval. Trouble is the size of this RRD file is 17 Mbytes. This may not seem as bad but one of the RRD drawbacks is that every time you add data to an RRD the whole file is written over so if you have 1000 metrics you can be potentially writing 17 GBs of data every 15 seconds. This may be a problem depending how many metrics you are keeping track of. There are alternatives which increase throughput such as storing RRDs in RAMdisk or using rrdcached. Alternatively you can opt to keep 2 weeks worth of data points with e.g.

"RRA:AVERAGE:0.5:1:81984"

which will result in size of about 650 kBytes per RRD file. Or you can do something else altogether. Flip side of RRD is that there are no indexes to maintain, no tables that need to be rotated.

**Update:** I was wrong about the whole RRD file needing to be updated. In retrospect it makes sense and I apologize for providing the wrong info. You can read comment from Tobi Oetiker (creator of rrdtool) in comments below for more detail. This is actually awesome news since there is very little downside in making larger RRDs.

As far as Ganglia you can modify the defaults in /etc/ganglia/gmetad.conf file. You can also use gmetad-python which allows you to write your own plugins and store metric data in both RRD format, SQL or any other storage engine of your choice.

More on RRDtool can be found here

[http://oss.oetiker.ch/rrdtool/tut/rrdtutorial.en.html](http://oss.oetiker.ch/rrdtool/tut/rrdtutorial.en.html)
