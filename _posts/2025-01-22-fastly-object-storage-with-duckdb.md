---
author: admin
comments: false
date: 2025-01-22 18:00:00+00:00
layout: post
slug: 
title: Using Fastly Object Storage with DuckDB
tags:
- DuckDB
- Fastly
---

DuckDB allows direct querying/importing of data from HTTP endpoints including S3 compatible storages. The `httpfs extension` currently lacks
direct support for [Fastly Object Storage](https://www.fastly.com/products/storage) however as it is S3 compatible all you need to do is 
modify the S3 type to support it. The only two changes are 
  * *ENDPOINT* needs to point to the Fastly endpoint for the region you want to use e.g eu-central.object.fastlystorage.app
  * *URL_STYLE* needs to use `path`

For example I have a bucket in `us-east` region and my config looks as follows.

<pre>
CREATE SECRET secret1 (
    TYPE S3,
    KEY_ID 'K******************',
    SECRET '*******************************',
    REGION 'us-east',
    ENDPOINT 'us-east.object.fastlystorage.app',
    URL_STYLE 'path'
);
</pre>

After that's done you can do things like

<pre>
SELECT * FROM 's3://my-test-bucket/data.csv';
</pre>
