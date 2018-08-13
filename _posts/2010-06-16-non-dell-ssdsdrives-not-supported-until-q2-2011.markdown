---
author: admin
comments: true
date: 2010-06-16 15:09:11+00:00
layout: post
slug: non-dell-ssdsdrives-not-supported-until-q2-2011
title: Non-Dell SSDs/drives not supported until Q2 2011
wordpress_id: 214
categories:
- Systems Management
tags:
- SSD
---

I am writing up this post so perhaps I can save some poor sysadmin from chasing their own tales. If you ever receive following error message using PERC H700 or H800 controllers

    
    Jun 15 14:00:17 db07 Server Administrator:  Storage Service EventID: 2335  Controller event log: PD 04(e0x20/s4) is  not supported:  Controller 0 (PERC H700 Integrated)
    Jun 15 14:00:18  db07 Server Administrator: Storage Service EventID: 2334  Controller  event log: Inserted: PD 05(e0x20/s5):  Controller 0 (PERC H700  Integrated)
    Jun 15 14:00:18 db07 Server Administrator: Storage  Service EventID: 2335  Controller event log: PD 05(e0x20/s5) is not  supported:  Controller 0 (PERC H700 Integrated)


It is due to following

[http://www.standalone-sysadmin.com/blog/2010/04/dell-reverses-position-on-3rd-party-drives/](http://www.standalone-sysadmin.com/blog/2010/04/dell-reverses-position-on-3rd-party-drives/  )

Please note this will not be fixed until Q2 2011.
