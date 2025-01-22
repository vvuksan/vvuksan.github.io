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

DuckDB allows direct querying of data from HTTP endpoints including S3 compatible APIs. The `httpfs extension` currently lacks
direct support for [Fastly Object Storage](https://www.fastly.com/products/storage) however you can simply modify the S3 type to support it
ENDPOINT and URL_STYLE. For example I have a bucket in `us-east` region and my config looks as follows.

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

After that's done I can do things like

<pre>
SELECT * FROM 's3://my-test-bucket/data.csv';
</pre>
