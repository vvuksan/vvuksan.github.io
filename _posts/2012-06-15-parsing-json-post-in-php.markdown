---
author: admin
comments: false
date: 2012-06-15 21:42:35+00:00
layout: post
slug: parsing-json-post-in-php
title: Parsing JSON POST in PHP
wordpress_id: 547
categories:
- PHP
---

I have an application that uses HTTP POST to submit a JSON encoded array. Basically there are no variables that are being submitted, just JSON. This causes $_REQUEST and $_POST arrays to get messed up where "random" parts of the JSON will end up as the key and rest as the value. Instead what you need to do is get contents of the request as input then decode it   e.g.

    
        $input = file_get_contents('php://input');
        $my_array = json_decode( $input, TRUE);
