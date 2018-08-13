---
author: admin
comments: true
date: 2010-06-27 17:01:03+00:00
layout: post
slug: velocity-conference-2010-takeaways
title: Velocity Conference 2010 takeaways
wordpress_id: 224
categories:
- Networking
tags:
- WPO CSS Javascript Mobile
---

[Velocity 2010](http://en.oreilly.com/velocity2010/) was an excellent conference. Following are my takeways from the conference. There is tons more but following are some of the things that made a good impression and are likely not hard to do


### Web performance optimization





	
  * Look at your Javascript. That is one of the major reasons for page slowness since Javascript download and parsing blocks other elements of the page to be loaded. Use [Google's Closure Compiler](http://code.google.com/closure/compiler/) to optimize Javascript by merging code and eliminating duplicate or unneeded functions. Check out [Anne Sullivan's Progressive Enhancement slides](http://www.monkey.org/~annie/ProgressiveEnhancement.html).

	
  * Yahoo release Boomerang which they describe as a piece of javascript that you add to your web pages, where it measures the performance of your website from your end user's point of view. It has the ability to send this data back to your server for further analysis.More details at [http://github.com/yahoo/boomerang](http://github.com/yahoo/boomerang)

	
  * Version your CSS/Javascript and set expire times for 10+ years. Check out slide 24 from [Theo Schlossnagle Scalable Internet Architectures slides](http://www.slideshare.net/postwait/velocity-2010-scalable-internet-architectures).

	
  * Use Cookies as a "distributed database". If you are concerned about security or tampering encrypt the cookies.

	
  * Use JQuery sparingly. It takes 200-300 ms to parse it. This is even worse in the mobile world.

	
  * Google rewrote their show_ads.js with ASWIFT which causes script loading to be asynchronous and not block other elements from loading. More about "[Don't Let Third Parties slow you down](http://en.oreilly.com/velocity2010/public/schedule/detail/15412)" and  [http://www.royans.net/arch/speeding-up-3rd-party-widgets-using-iframes/](http://www.royans.net/arch/speeding-up-3rd-party-widgets-using-iframes/)




### Mobile performance optimization


Most of the recommendations have been taken off [Maximiliano Firtman](http://firt.mobi/)'s Mobile Web High Performance. You can [view slides here](http://www.mobilexweb.com/blog/mobile-web-high-performance).



	
  * Avoid JQuery unless you really need it. Check out slide 90. It takes 1.8 seconds on iPhone and 4 seconds on Android to download and parse JQuery. Use mobile optimized frameworks such as baseJS and XUI

	
  * Avoid DNS lookups and minimize number of requests since they are slow

	
  * Embed CSS and Javascript on the home page. After onload download external CSS and JS.

	
  * Use inline images (slide 56) and [pictograms](http://pukupi.com/post/1964)

	
  * Avoid redirects

	
  * Use native constructs especially for Webkit browsers e.g. -webkit-text-stroke

	
  * Keynote announced their Mobile Testing tool for desktops that looks promising [http://mite.keynote.com/](http://mite.keynote.com/)




### SSL/Security





	
  * According to [Google SSL](http://en.oreilly.com/velocity2010/public/schedule/detail/14217) overhead these days is pretty minimal. Around 1% on today's servers.

	
  * Pet peeve about the presentation is they were advising everyone to use less secure key lengths ie. 1024 bits and RC4 cipher to improve performance. It is true that adding SSL to insecure connections is certainly an improvement but it should be qualified. E-mail probably fine. Financial sites probably bad.




### Scalability





	
  * [Hidden Scalability Gotchas in Memcached and Friends](http://en.oreilly.com/velocity2010/public/schedule/detail/13046) by Neil Gunther (author of Guerilla Capacity Planning) and Shanti Subramanyam discussed their findings around memcached. They used quantitative analysis to analyze different memcache versions. Based on their analysis using Neil's model memcache 1.4.5 has higher contention than 1.2.8.




### Culture





	
  * Thoroughly enjoyed John Rauser's [Creating Cultural Change](http://en.oreilly.com/velocity2010/public/schedule/detail/11793)


