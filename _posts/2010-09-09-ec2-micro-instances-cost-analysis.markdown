---
author: admin
comments: true
date: 2010-09-09 22:02:25+00:00
layout: post
slug: ec2-micro-instances-cost-analysis
title: EC2 micro instances cost analysis
wordpress_id: 367
categories:
- Cloud Computing
---

Amazon today announced addition of EC2 micro instances which is their smallest instance size coming with 613 MB RAM and priced at $0.02/hour. You can read more about the announcement here

[http://aws.typepad.com/aws/2010/09/new-amazon-ec2-micro-instances.html](http://aws.typepad.com/aws/2010/09/new-amazon-ec2-micro-instances.html)

There is a wrinkle though. There is no local (ephemeral) storage so you need to use [EBS backed volumes](http://aws.typepad.com/aws/2009/12/new-amazon-ec2-feature-boot-from-elastic-block-store.html). EBS is charged at $0.10/GB per month along with the charge of $0.10/1 million I/O requests to the volume. That is actually a reasonably good idea since it likely cuts down on I/O subsystem abuse since if you start abusing I/O it will cost you. That said I thought I would run a quick cost analysis to determine how much would it cost to actually run an instance. I have a personal server I use for handling my family's e-mail, blog and personal web sites. It gets little traffic. I use roughly 30 GB of storage. To find out the number of I/O ops I ran following command

    
    > cat /proc/diskstats | egrep "sd[a-b] " | awk '{print $4" "$8}'
    154756 3576927
    773387 1844813


This lists number of both read and write ops for both drives in my machine. It adds to about 6 mil iops. Machine was last rebooted 7 days ago making this 25 mil iops per month. On the outbound network traffic side I have consumed 5 GB of traffic so far so => 20 GB per month (charged at $0.15/GB).

Thus the cost breaks down like this per month

Instance cost (30*24*$0.02) = $14.40

EBS storage charge ( 30 * $0.10) = $3.00

EBS I/O ops charge ( 25 * $0.10)  = $2.50

Outbound network traffic ( 20 * $0.15 ) = $3.00

**Total: $22.90**

Not too bad. A word of warning though. Since these micro instances come with only 613 MB of RAM if you load even a handful of services such as a mySQL database, web or app server you may end up swapping causing your EBS I/O ops charges to go up. I doubt these would be enormous however depending on the level of swapping they could be 25, 50% or 100% higher than what you planned for. Obviously EBS has some nice features such as persistency, snapshotting and ability to boot instances automatically after a failure however it may come with unanticipated cost.

**Update**: Some have pointed out that instance costs can be even lower if you reserve (assuming 1-yr commit micro instances are $115/year vs. $172 non-reserved). That is true however as I point out the biggest X factor in the whole equation is EBS charges. It's nothing that will break the bank however I prefer having idea upfront what the cost is. If your use case is a DNS server, mail server, Nagios checker than this fits the bill well however if you plan to use a ticketing system, wiki that uses a DB backend you will likely exceed memory footprint and start swapping.
