---
author: admin
comments: true
date: 2010-05-27 13:39:00+00:00
layout: post
slug: devops-homebrew-part-deux
title: Devops homebrew part deux
wordpress_id: 162
categories:
- Systems Management
tags:
- Devops
---

This is the second part to the [devops homebrew](http://blog.vuksan.com/2010/04/09/devops-homebrew/) post.

I forgot couple things in my first post so here are couple other observations


### **Change is an ongoing process**


All the changes I talked about in the first post took a long time. It took more than a year to get issues assessed, discussed, designed, implemented and tested so don't expect quick progress. It's like an open heart surgery where you don't have time stop everything and start from scratch.


### **No hardcoded paths**


Perhaps this one should be obvious however it is really important to make the app relocatable ie. app should assume all the files it needs are within it's container. This means that every file reference should be relative to the base container directory e.g. all the WARs and configuration files should be placed in /run/base and startup script would pass that as a variable ie. -DBASEDIR=/run/base. Application should then use BASEDIR instead of /run/base.


### Tools, tools, tools


One of the critical operations responsibilities is providing and building tools for use by other groups such as technical support, development, QA etc. This goes beyond using tools such as configuration management and deployment but also building tools that enable other groups to do their jobs more effectively. For instance at one job we used to interface to hundreds of external LDAP/IMAP sources for authentication/authorization purposes. This was fraught with problems since often these services would e.g. misconfigure firewalls (not whitelist the right IP), have expired or self-signed SSL certificates, use wrong LDAP base DNs etc. This would chew up a lot of professional services, dev and ops time since looking at the application logs often gave incomplete answers. Also it could take couple iterations to fix the problem chewing up even more time. We ended up building a simple web page that enabled professional services to quickly validate the service ie. does DNS resolve, can I open up a TCP connection to the target port, is SSL certificate expired etc. This greatly reduced work load and time to resolution. In another job technical support would often need production settings however due to compliance reasons couldn't have unfettered access to the systems. For them we built a web app that allowed read-only view to the needed settings. I'm sure you can think of other cases where little automation can yield you huge efficiencies.


### Use underpowered QA environments


This may be controversial since lots of people are of the opinion that you should try to have as close to the exact replica of production in QA. This is true if you are doing performance tests however if you have an underpowered environment some issues are likely to crop up that otherwise wouldn't. It is very hard to simulate production load so having underpowered environments gives you valuable data points. For example our primary QA environment ran on couple virtualized servers with modest disk space allocation ie. 10 GB. On more than one occasion we caught serious code deficiencies when the growing query log (turned on in QA) triggered low disk space alerts. If we had bigger disks we may have missed these. This doesn't preclude having a separate environment just for running performance test just use the underpowered environment for everything else.


### Dev vs ops


There is often conflict between dev and ops due to stereotypes, poor communication but very often misaligned business goals. For instance I have very often seen/experienced conflict with devs when they were under intense pressure to deliver a feature on a tight deadline. This often happens in startups that cater to large businesses, universities or government organizations where a large sales deal is contingent on a particular feature being implemented. It leads to poor implementation, QA, production issues etc. which coupled with poor division of labor causes frustration and resentment. Being woken up numerous times in the middle of night due to a production issue quickly wears people out. Therefore it is important to strike a balance between ops and dev goals and overall business goals.

One of the possible approaches is to get together and discuss following issues



	
  * Ops, dev and QA should jointly assess new product functionality and how it affects each of these groups. Very often product management and sales and marketing will discuss new features only with dev who may not appreciate the difficulty of certain ops decisions.

	
  * Division of responsibility - discuss whose responsibility is to fix things when they break. There is a spectrum here where ops can do first level troubleshooting then hand it off to developers to developers running and deploying in production and ops providing a supportive role running services and tools that enable the application

	
  * Off hours coverage - this is probably the most contentious one since no one likes being woken up at night however developers should be on hook for "pager duty". It doesn't have to be regularly but at least once in a while. That is really only way for them to walk in ops shoes. For some organizations this may be a non-issue since their stuff never breaks in off hours ;-).

	
  * Ops should involve devs in running the production by educating them about monitoring and performance gathering systems so that they can see effect of their coding first hand. For instance you can implement "monitoring duty" where each week someone different from either dev or ops team is tasked to review performance metrics looking for things that are out of whack.

	
  * Discuss how you can make each other life's easier. There are always areas where you can complement each others skills and create something that helps everyone.

	
  * Most important don't forget that a dose of humility goes a long way :-).


