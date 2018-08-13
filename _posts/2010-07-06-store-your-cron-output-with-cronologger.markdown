---
author: admin
comments: true
date: 2010-07-06 12:32:41+00:00
layout: post
slug: store-your-cron-output-with-cronologger
title: Store your cron output for analysis and correlation with cronologger
wordpress_id: 244
categories:
- Linux
- Monitoring
tags:
- Cron Linux CouchDB
---

For the longest time I have wanted to get rid of dozen or so cron messages I receive every morning about things like DB backups, DB cleanups/vacuums, reporting etc. There are a number of solutions out there to help you manage the cron spam such as [cronic](http://habilis.net/cronic/), [shush](http://web.taranis.org/shush/) and [cronwrap](http://www.uow.edu.au/~sah/cronwrap.html). They help by e-mailing you only if there is a problem however don't store the cron output itself. To get around that issue I have developed cronologger which can be downloaded from

[http://github.com/vvuksan/cronologger](http://github.com/vvuksan/cronologger)

Cronologger is a BASH script that stores all the cron output into a database. I am using [CouchDB](http://couchdb.apache.org/) since it is a great document oriented database that allows me to add attachments (blobs) to a document. I assume it would not be hard to use MongoDB, Riak and others.

Some of the benefits of this utility are



	
  * Reduce cron spam

	
  * Provide the ability to correlate adverse affects by overlaying cron events on e.g. Ganglia graphs

	
  * Provide a better report of all the batch jobs that ran, diff them with past jobs if they should look the same, etc.

	
  * Provide the ability to easily view what is currently running on the whole infrastructure ie. job_duration < 0

	
  * Review historical output


I am still working on web GUI for most of these things. I will gladly accept patches and new contributions.

Tip: To get view a list of documents in a CouchDB database you can use the _utils view e.g. http://localhost:5984/_utils/
