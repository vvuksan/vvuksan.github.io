---
author: admin
comments: true
date: 2010-09-01 12:26:52+00:00
layout: post
slug: install-openstack-nova-easily-using-chef-and-nova-solo
title: Install Openstack Nova easily using Chef and Nova-Solo
wordpress_id: 359
categories:
- Cloud Computing
- Linux
- Systems Management
---

Inspired by [Cloudscaling's Swift-Solo](http://github.com/cloudscaling/swift-solo) and being excited about being able to create my own cloud I am announcing the Nova-Solo project. [Openstack](http://openstack.org/) Nova is the Compute portion of the project trying to build open source stack to run Amazon EC2 type service. Nova-Solo is a set of [Opscode Chef](http://Opscode.com/chef/) recipes that allow you to quickly get most parts of the Nova stack up and running. You can fetch it from Github at

[http://github.com/vvuksan/nova-solo](http://github.com/vvuksan/nova-solo)

At this time Nova-Solo is targeted for Ubuntu 10.04 and it relies on [Soren Hansen's](https://wiki.ubuntu.com/SorenHansen) package repository to install all of the necessary packages. Following Nova services are installed



	
  * Cloud controller

	
  * Object store

	
  * Volume store

	
  * API server

	
  * Compute Server


Soren's package archive is a bit outdated so some of the things don't work. For example you can create users, generate credentials, upload files into buckets but you can't register the image. Soren has said he is in the process of building new packages and I am also in the process of doing the same so hopefully things improve quickly. Nova code is definitely alphaish so beware. To get started use git to clone the nova-solo repository and off you go

    
    git clone git://github.com/vvuksan/nova-solo.git


In the future as things stabilize we'll be making adjustments to support multiple compute servers (pieces for it are already in Nova-Solo), support other distributions like RHEL/Centos, etc.
