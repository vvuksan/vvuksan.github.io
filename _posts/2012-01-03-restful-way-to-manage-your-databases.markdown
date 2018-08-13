---
author: admin
comments: true
date: 2012-01-03 03:43:35+00:00
layout: post
slug: restful-way-to-manage-your-databases
title: RESTful way to manage your databases
wordpress_id: 490
categories:
- DevOps
- Systems Management
---

I have a need in my development environment to easily create/drop mySQL databases and users. Initially I was gonna implement a simple hacky HTTP GET method but was dissuaded by [Ben Black ](https://twitter.com/b6n)from doing so. He suggested I write a proper RESTful interface. Without further ado I present to you dbrestadmin

[https://github.com/vvuksan/dbrestadmin](https://github.com/vvuksan/dbrestadmin)

It is my first foray into writing RESTful services so things may be rough around the edges. However it allows you to do following



	
  * manage multiple database servers

	
  * create/drop databases

	
  * list databases

	
  * create/drop users

	
  * list users

	
  * give user grants

	
  * view grants given to the user

	
  * view database privileges on a particular database given to a user


For example need to create a database called testdb on dbserver ID=0 use this cURL command

    
    curl -X POST http://myhost/dbrestadmin/v1/databases/0/dbs/testdb


Create a user test2 with password test

    
    curl -X POST "http://localhost:8000/dbrestadmin/v1/databases/0/users/test2@localhost" -d "password=test"


Give test2 user all privileges on testdb

    
    curl -X POST "http://localhost:8000/dbrestadmin/databases/0/users/test2@'localhost'/grants" -d "grants=all privileges&database=testdb"


There is more. You can see all of the methods here

[https://github.com/vvuksan/dbrestadmin/blob/master/API.md](https://github.com/vvuksan/dbrestadmin/blob/master/API.md)

Improvements and constructive criticism welcome
