---
author: admin
comments: true
date: 2009-11-05 18:16:46+00:00
layout: post
slug: dont-let-mysql-substitute-engines
title: Don't let mySQL substitute engines
wordpress_id: 83
categories:
- Systems Management
---

Word of warning to all who use mySQL (yes you poor souls). By default mySQL 5.0 and 5.1 will substitute storage engines if the one you requested is not available. It doesn't happen too often but when it does happen it is quite bad. For instance when setting up a new mySQL database something went wrong during creation of InnoDB logs and thus mySQL decided to DISABLE InnoDB storage. Unfortunately this was not caught and DBs were built that really needed InnoDB storage engine since they required foreign keys and other fun stuff. In their "awesomeness" mySQL developers decided that the default behavior should be to simply substitute (replace) InnoDB with myISAM. There is a warning however no error message is displayed and an import will continue unabated. Thus in my case things worked for a while until oddities were discovered which were traced back to the engine substitution. Unfortunately at that point it is fairly difficult to fix the problems since some of the constraints may be broken.

To avoid such a situation make sure you add following statement to my.cnf

sql_mode="NO_ENGINE_SUBSTITUTION"

To verify what engines are active on mySQL shell prompt type

SHOW ENGINES
