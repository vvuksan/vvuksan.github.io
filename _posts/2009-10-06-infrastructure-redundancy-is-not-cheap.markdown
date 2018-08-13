---
author: admin
comments: true
date: 2009-10-06 11:43:02+00:00
layout: post
slug: infrastructure-redundancy-is-not-cheap
title: Infrastructure redundancy is not cheap
wordpress_id: 67
categories:
- Cloud Computing
- Systems Management
---

There was quite a discussion on Twitter about the BitBucket outage which initially appeared to be failure of Amazon EC2/EBS. More about the outage can be found [here](http://blog.bitbucket.org/2009/10/04/on-our-extended-downtime-amazon-and-whats-coming/). Brett Piatt was kind enough to write up his view of the situation

[http://www.bretpiatt.com/blog/2009/10/03/availability-is-a-fundamental-design-concept/](http://www.bretpiatt.com/blog/2009/10/03/availability-is-a-fundamental-design-concept/)

In principal I do agree with his suggestions and his conclusion ie. that availability is a fundamental design concept. I do however disagree that "warm" redundancy is cheap. In my own view and experience redundancy is extremely expensive if you are going to do it right. Redundancy is not just being able to add more hardware, systems and monitoring software and failover policies but a matter of process where you continuously have to make sure that the redundancy works. For instance successful backup strategy doesn't consist of simply getting a backup device yet never testing the backups by doing an actual restore. As many organizations have discovered backups do break, media gets corrupted, etc. and you can suffer a devastating blow. So if you want to do redundancy right you have to invest lots and lots of time practicing. For example running fire drills is a useful tool or doing periodic site failovers ie. run on site A for two weeks, then during low traffic times failover to site B, run for two week then back to site A and on and on. That certainly ain't cheap.

I'd also point out that "warm" redundancy is in lots of instances riskier than "hot" redundancy since you may discover that redundancy doesn't work when you have to failover whereas in "hot" redundancy issues may crop up much earlier allowing you to stay on top more readily.

That said the discussions over how you are responsible for your own availability reminds of "individual responsibility" (for my international readers this is something that is a hot topic in the United States). Sure you should "own" your redundancy however that may often be impractical or too expensive. Not everyone is blessed with copious resources.
