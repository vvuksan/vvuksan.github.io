---
author: admin
comments: true
date: 2009-12-01 19:09:58+00:00
layout: post
slug: quick-way-to-determine-ssl-certificate-expiration
title: Quick way to determine SSL certificate expiration
wordpress_id: 85
categories:
- Networking
- Systems Management
---

If you need a quick way to determine when a certain SSL certificate expires you can utilize following approaches. In both examples server I am trying to check is called webserver.domain.com.

If you have Nagios plugins installed you could type

    
    # /usr/lib/nagios/plugins/check_http -p 443 -S -C 15 webserver.domain.com
    CRITICAL - Certificate expired on 11/01/2009 11:23.


That's easy. However what if you don't have Nagios plugins. In that case you can do the same with OpenSSL and s_client. Look for notAfter field.

    
    # echo | openssl s_client -connect webserver.domain.com:443 | openssl x509 -noout -dates
    ...
    notBefore=Nov  1 11:23:30 2008 GMT
    notAfter=Nov  1 11:23:30 2009 GMT


Easy :-).
