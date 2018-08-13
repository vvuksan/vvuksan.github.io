---
author: admin
comments: true
date: 2011-08-22 21:33:49+00:00
layout: post
slug: using-jenkins-as-a-cron-server
title: Using Jenkins as a Cron Server
wordpress_id: 466
categories:
- Linux
- Systems Management
---

There are a number of problems with cron which cause lots of grief for system administrators with big ones being manageability, cron-spam and auditability. To fix some of these issues I have lately started using [Jenkins](http://jenkins-ci.org/). [Jenkins](http://jenkins-ci.org/) is an open source Continuous Integration server it has lots of features that make it a great cron replacement for a number of uses. These are some of the problems it solves for me


### Auditability


Jenkins can be configured to retain logs of all jobs that it has run. You can set it up to keep last 10 runs or you can set it up to keep only last 2 weeks of logs. This is incredibly useful since sometimes jobs can fail silently so it's useful to have the output instead of sending it to /dev/null.


### Centralized management


I have my most important jobs centralized. I can export all Jenkins jobs as XML and check it into a repository. If I need to execute jobs on remote hosts I simply have Jenkins ssh and execute command remotely. Alternatively you can use [Jenkins slaves](https://wiki.jenkins-ci.org/display/JENKINS/Distributed+builds).


### Cron Spam


Cron spam is a common problem with solutions such as [this](http://habilis.net/cronic/), [this](http://iamthewalr.us/blog/2007/10/howto-make-cron-not-spam-you-to-death/) and [this](http://blog.dynamichosting.biz/2010/11/01/stop-crond-from-sending-e-mails/#more-83). To avoid this condition I only have Jenkins alert me when a particular job fails ie. a job exits with return code other than 0.  In addition you can use the awesome [Jenkins Text Finder](https://wiki.jenkins-ci.org/display/JENKINS/Text-finder+Plugin) plugin which allows you to specify words or regular expressions to look for in console output. They can be used to mark a "job" unstable. For example in text finder config I checked


X Also search the console output




and specified




Regular expression ([Ee]rror*).*


This has saved our bacon since we used the automysqlbackup.sh script which "swallows" up the errors codes from the mysqldump command and exits normally. Text Finder caught this

`mysqldump: Error 2020: Got packet bigger than 'max_allowed_packet' bytes when dumping table `users` at row: 234
`

Happily we caught this one on time.


### Job dependency


Often you will have job dependencies ie. main backup job where you first dump a database locally then upload it somewhere off-site or to the cloud. The way we have done this in the past is to leave a sufficiently large window between the first job and consecutive job to be sure first job has finished. This says nothing about what to do if the first job fails. Likely the second one will too. With Jenkins I no longer have to do that. I can simply tell Jenkins to trigger "backup to the cloud" once local DB backup concludes successfully.


### Test immediately


While you are adding a job it's useful to test whether job runs properly. With cron you often had to wait until the job executed at e.g. 3 am in the morning to discover that PATH wasn't set properly or there was some other problem with your environment. With Jenkins I can click Build Now and job will run immediately.


### Easy setup


Setting up jobs is easy. I have engineers set up their own job by copying an existing job and modifying it to do what they need to do. I don't remember last time someone asked me how to do it :-).


### What I don't use Jenkins for


I don't use Jenkins to run jobs that collect metrics or anything that has to run too often.


