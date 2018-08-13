---
author: admin
comments: true
date: 2010-07-15 01:49:19+00:00
layout: post
slug: couchdb-views-creation-problems
title: CouchDB views creation problems
wordpress_id: 248
categories:
- Systems Management
tags:
- CouchDB
---

I have had a frustrating time creating views in CouchDB using curl. Executing following command I would get

    
    $ curl -s -X PUT -H "text/plain;charset=utf-8" -d cronview.json http://localhost:5984/cronologger/_design/cronview
    {"error":"bad_request","reason":"invalid UTF-8 JSON"}


I checked and rechecked JSON, used the same JSON using CouchDB's Futon to no avail. Finally I found the answer here

[http://stackoverflow.com/questions/2461798/error-about-invalid-json-with-couchdb-view-but-the-jsons-fine](http://stackoverflow.com/questions/2461798/error-about-invalid-json-with-couchdb-view-but-the-jsons-fine)


The [`-d` option of curl](http://curl.haxx.se/docs/manpage.html#-d--data) expects the actual data as the argument!




If you want to provide the data in a file, you need to prefix it with `@`:




    
    <code>curl -X PUT -d @keys.json  $CDB/_design/id
    </code>
