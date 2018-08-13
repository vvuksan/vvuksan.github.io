---
author: admin
comments: true
date: 2010-04-09 13:58:05+00:00
layout: post
slug: devops-homebrew
title: Devops homebrew
wordpress_id: 143
tags:
- Deployment
- Ops
---

There has been quite a bit of discussion about Devops and what it means. [@blueben](http://twitter.com/blueben/status/11720129187) has suggested we start a Devops patterns cookbook so people can learn what worked or didn't work. This is the description of the environment we implemented at a previous job. Some of these things may or may not work for you. I will try to keep it short.


### Environment background


7 distinct applications/products that had to be deployed and tested ie. base/core application, messaging platform, reporting app etc. All applications were Java based running on either Tomcat or Jboss.


### Application design for deployment


These are some of the key points



	
  1. Application should have a sane 	default configuration options. Any option should be overrideable by 	an external file. In most cases you only need to 	override database credentials (host, username, password). Goal is to be able to use the same binary across multiple environments.

	
  2. Application should expose key internal metrics. We 	for instance asked for a simple key/value pairs web page ie.   	JMSenqueue=OK etc. This is important because there are lots 	of things that can break inside the application which external 	monitoring may miss like JMS message can't be enqueued, etc.

	
  3. Keep release notes actions to a minimum. Release notes are often not followed or partially followed thus make sure point 1. is followed and/or try to automate everything else.




### Continuous Integration


We used CruiseControl for Continuous Integration. It was used solely to make sure that someone didn't break the build.


### Creating releases


Developers are in charge of building and packaging releases. This primarily because QA or Ops will not know what to do if a build fails (this is Java remember). Each release has to be clearly labeled with the version and tagged in the repository. For example Location 1.1.5 will be packaged as location-1.1.5.tar.gz. Archives should contain only WAR (Tomcat) or EAR (Jboss) files and DB patch files. Releases are to be deposited into an appropriate file share ie. /share/releases/location.


### Deployment


In order to eliminate most manual deployment steps and support all the different applications we decided to write our own deployment tool. First we started off with a data model which roughly broke down to



	
  1. Applications – can use different 	app server containers ie. Tomcat/JBoss,  may/will have configuration 	files that can be either key/value pairs or templates. For every 	application we also specified a start and stop script (hotdeploy was 	not an option due to bad experiences with our code).

	
  2. Domains/Customers – we wanted a 	single Dashboard that would allow us to deploy to multiple 	environments e.g. QA staging (current release), QA development (next 	scheduled release), Dev playbox, etc. Each of these domains had 	their own set of applications they could deploy with their own 	configuration options


First we wrote a command line tool that was capable of doing something like this

    
    $ deployer –version 1.2.5 –server web10 –domain joedev –app base –action deploy<span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"> </span>


What this would do is



	
  1. Find and unpack the proper app 	server container e.g. jboss-4.2.3.tar.gz

	
  2. Overlay WAR/EAR files for the name 	version e.g. base-1.2.5.tar.gz

	
  3. Build configuration files and 	scripts

	
  4. Stop the server on the remote box 	(if it's running)

	
  5. Rsync the contents of the packaged release

	
  6. Make sure Apache AJP proxy is 	configured to proxy traffic and do Apache reload

	
  7. Start up the server


One of the main reason we started off with a command line tool is that we could easily write batch scripts to upgrade whole set of machines. This was borne out of pain of having to upgrade 200 instances via a web GUI at another job.

Once deployer was working we wrote a web GUI that interfaced with it. You could do things like View running config (what config options are actually on the appserver), Stop, Restart, Deploy (particular version), Reconfig (apply config changes) and Undeploy. We also added the ability to change or add configuration options to the application specific override files. Picture is worth thousand words. This is a tiny snippet how it approximately looked for one domain

[![](http://blog.vuksan.com/wp-content/uploads/2010/04/deployer-11.png)](http://blog.vuksan.com/wp-content/uploads/2010/04/deployer-11.png)

This was a big win since QA or developers no longer needed to have someone from ops deploy software.


### DB patching


Another big win was "automated" DB patching. Every application would have a table called Patch with a list of DB patches that were already applied. We also agreed that every app would have dbpatches directory in the app archive which would contain a list of patches named with version and order in which they should be applied e.g.



	
  * 2.54.01-addUserColumn.sql

	
  * 2.54.02-dropUidColumn.sql


During deployment startup script would compare contents of the patch table and a list of dbpatches and apply any missing ones. If the patch script failed e-mail would be sent to the QA or dev in charge of particular domain.

A slightly modified process was used in production to try to reduce down time ie. things like adding a column could be done at any time. Automated process was largely there to make QA's job easier.


### QA and testing


When a release was ready QA would deploy the release themselves. If there was a deployment problem they would attempt to troubleshoot it themselves then contact the appropriate person. Most of the times it was an app problem ie. particular library didn't get commited etc. This was a huge win since we avoided a lots of "waterfall" problems by allowing QA to self-service themselves.


### Production


Production environment was strictly controlled. Only ops and couple key engineers had access to it. Reason was we tried to keep the environment as stable as possible. Thus ad hoc changes were frowned upon. If you needed to make a change you would either have to commit a change into the configuration management system (puppet) or use the deployment tool.


### Production deployment


The day before the release QA would open up a ticket listing all the applications and versions that needed to be deployed. On the morning of the deployment (that was our low time) someone from ops, development and whole QA team engaged in deploying the app and resolving any observed issues.


### Monitoring


Regular metrics such as CPU utilization, load etc. were collected. In addition we kept track of internal metrics and set up adequate alerts. This is an ongoing process since over time you discover what your key metrics are and what their thresholds are ie. number of threads, number of JDBC connections etc.


### Things that didn't work so well or were challenging





	
  1. One of the toughest parts was 	getting developers' attention to add "goodies" for ops. 	Specifically exposing application internals was often put off until 	eventually we would have an outage and lack of having the metric 	resulted in extended outage.

	
  2. Deployment tool took couple tries 	to get right. Even as it was there were couple things I would have 	done differently ie. not relying on a relational database for the 	data model since it made it difficult to create diffs (you had to 	dump the whole DB). I'd likely go with JSON so that diffs could be easily reviewed and committed.

	
  3. Other issues I can't recall right now :-)




### Wrapup


This is the shortest description I could write. There are a number of things I glossed over and omitted so that this is not too long. I may write about those on another occasion. Perhaps the key take away should be that Ops should focus on developing tools that either automate things or allow its customers (QA, dev, technical support, etc.) to self-service themselves.

**Update**: There is a [second part to this posts](http://blog.vuksan.com/2010/05/27/devops-homebrew-part-deux/)
