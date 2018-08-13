---
author: admin
comments: true
date: 2010-08-12 17:55:36+00:00
layout: post
slug: deployment-rollback
title: Deployment rollback
wordpress_id: 328
categories:
- DevOps
- Systems Management
---

This is a question that often comes up in deployment discussion. How do you rollback in case of a "bad" deploy ? Bad deploy can be any of the following



	
  * Site completely broken

	
  * Significant performance degradation

	
  * Key feature(s) broken


There are obviously a number of ways to deal with this issue. You could put up a notice on the site that x and y feature is broken while you work to fix it. Same with performance degradation. Let's however deal with rollback ie. you decided (determined by a number of different factors) that the stuff you just deployed is broken and you should roll back to a previous last know version. In such a case you would

	
  * Undo any configuration changes you may have applied (often none)

	
  * Deploy last known good version that worked. This is one of the reasons why I prefer using labelled binary packages. I simply instruct the deployment tool to install version 1.5.2 which was last good version and off we go.


The only caveat are database changes. In general you can't easily undo DB changes especially in the situations where you discover a deployment problem couple hours after deployment has taken place since by then users may have added new posts, changed their profiles etc. It would be a major effort to undo all DB changes, evaluate newly added data and whether it needs to be changed. That said DB changes are usually not a problem if you follow these easy steps

	
  1. Don't do any column drops immediately after the release. You can do those in QA but in production those can wait. In most cases they only take up space. I have heard of places that would first zero out then drop "unused" columns once a quarter or so.

	
  2. Related to 1. never ever use SELECT * since if you drop or add a column your code may break during roll back

	
  3. If there are data changes you have to do ie. update carrier set name="AT&T" where name="Cingular", have the reverse SQL statement ready as the insurance policy. Those are quite easy to implement.

	
  4. You don't have to worry about added tables since older version will not use them.

	
  5. You don't have to worry about added columns provided you don't do 2. and have not placed constraints ie. NOT NULL. In that case you may need to adjust those or drop them during rollback.


The wildcard in all this is added or removed constraints ie. new foreign keys. There is no single solution for this one. Perhaps the right policy is to discuss constraints prior to deployment and have a plan ready on what to do. Good luck.
