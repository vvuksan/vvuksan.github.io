---
author: admin
comments: true
date: 2010-08-19 17:59:47+00:00
layout: post
slug: skippingmysql-replication-errors
title: Skipping MySQL replication errors
wordpress_id: 339
categories:
- Systems Management
tags:
- mySQL
---

I was talking to my buddy Jeff Buchbinder and he mentioned that he recently added following to mySQL in order to reduce mySQL replication breakages

    
    slave-skip-errors=1062,1053,1146,1051,1050


What this does is not stop replication in case following errors are encountered


Error: `1050` SQLSTATE: `42S01` ([`ER_TABLE_EXISTS_ERROR`](http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html#error_er_table_exists_error))




Message: Table '%s' already exists




Error: `1051` SQLSTATE: `42S02` ([`ER_BAD_TABLE_ERROR`](http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html#error_er_bad_table_error))




Message: Unknown table '%s'




Error: `1053` SQLSTATE: `08S01` ([`ER_SERVER_SHUTDOWN`](http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html#error_er_server_shutdown))




Message: Server shutdown in progress




Error: `1062` SQLSTATE: `23000` ([`ER_DUP_ENTRY`](http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html#error_er_dup_entry))




Message: Duplicate entry '%s' for key %d




Error: `1146` SQLSTATE: `42S02` ([`ER_NO_SUCH_TABLE`](http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html#error_er_no_such_table))




Message: Table '%s.%s' doesn't exist


This will avoid the very common primary key collisions and "temporary tables aren't there" problems. Writing this down for posterity. Use with caution.

Marius Ducea has a post about it as well

[http://www.ducea.com/2008/02/13/mysql-skip-duplicate-replication-errors/](http://www.ducea.com/2008/02/13/mysql-skip-duplicate-replication-errors/)
