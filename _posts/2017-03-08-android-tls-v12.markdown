---
author: admin
comments: false
date: 2017-03-09 18:00:00+00:00
layout: post
slug: android-4-tls-v12-built-in-browser-secure-connection-issues
title: Android 4.x TLS v1.2 built-in browser secure connection issues 
tags:
- Android
- TLS
- SNI
---

Recently at Fastly we have been gradually turning off TLS v1.0 and v1.1 support due to PCI mandate to deprecate
them. You can read about the deprecation policy [here](https://www.fastly.com/blog/phase-two-our-tls-10-and-11-deprecation-plan).

We also recently received couple reports from customers about some of the Android 4.x users not being able to access some of these end
points. During the investigation I found following SSLLabs issue

[https://github.com/ssllabs/ssllabs-scan/issues/258](https://github.com/ssllabs/ssllabs-scan/issues/258)

which had a pointer to this post about different vendors packaging a version of Google Chrome as their own built in browser

[http://www.quirksmode.org/blog/archives/2015/02/chrome_continue.html](http://www.quirksmode.org/blog/archives/2015/02/chrome_continue.html)

Unfortunately it appears that some vendors notably Samsung standardized on version of Chrome which did not have TLS v1.2 support e.g.
Chrome 28. Can I Use site has a nice table of TLS v1.2 support 

[http://caniuse.com/#search=tls%201.2](http://caniuse.com/#search=tls%201.2)

This is clearly a major hassle as it may force you to keep TLS 1.0/1.1 around for longer than you'd like or educate users to install
latest Google Chrome from the Play Store. To get a better understanding what the experience may look like is I tested it on my Android
4.2 table and this is what it it looks like

This is what the built-in browser capabilities are

[![Android 4.2 Built-in Browser capabilities](/assets/android_4.2_built_in_browser_capability.png)](/assets/android_4.2_built_in_browser_capability.png)

Unfortunately this will result in a very nasty error that says secure connection cannot be established

[![Android 4.2 Built-in Browser error](/assets/android_4.2_built_in_browser_tlsv12_error.png)](/assets/android_4.2_built_in_browser_tlsv12_error.png)

Same device with Google Chrome installed passes the capability test with flying colors

[![Android 4.2 Chrome browser capabilities](/assets/android_4.2_chrome_browser_capability.png)](/assets/android_4.2_chrome_browser_capability.png)

