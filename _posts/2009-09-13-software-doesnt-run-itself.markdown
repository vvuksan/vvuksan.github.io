---
author: admin
comments: true
date: 2009-09-13 23:11:58+00:00
layout: post
slug: software-doesnt-run-itself
title: Software doesn't run itself
wordpress_id: 49
---

Perhaps I should no longer be surprised but I am by the article mentioned in this blog post

[http://www.nakedcapitalism.com/2009/09/another-lehman-mess-no-one-can-run-the-software.html](http://www.nakedcapitalism.com/2009/09/another-lehman-mess-no-one-can-run-the-software.html)

In particular this


Once it went bankrupt, the staff who supported these systems “evaporated”, according to Steven O’Hanlon, president of Numerix, a pricing and valuation company which is working with Lehman Brothers Holding Inc to unwind the derivatives portfolio.



These days computer systems are the blood of your company so allowing critical technical staff to simply "evaporate" is mind boggling. Granted company imploded but still I would think that someone should have figured out going into bankruptcy that they should set aside money to pay for their maintenance.

Ultimate problem as pointed out in the blog post on Naked Capitalism that documentation is usually skimped on since it "doesn't provide value". Although I would also add that when people say "code is documented" they don't usually mention their systems infrastructure is documented. That can sometimes be even bigger impediment. At a previous job there was a Perl CGI script that most people didn't know about and even fewer understood. If that script didn't work our whole load balancing infrastructure would "mysteriously" fail since app servers wouldn't register themselves to web servers and leading to a full blown outage. It was such an obscure "feature" that you could literally spend weeks chasing other avenues since this was so non-obvious.

Also I would not take comfort in having source code to an application. Lot of customers of startups will write in their contracts that if a startup goes bust they get access to the source code. That may sound nice but it doesn't mean you will necessarily be able to run it. There are so many "secret" recipes, undocumented workarounds that are often involved in running most complex pieces of software that you should really be cautious.

In closing if you care that your software runs make sure you keep at least couple folks who have run it around.




http://www.nakedcapitalism.com

/2009/09/another-lehman-mess-no-one-can-run-the-software.html
