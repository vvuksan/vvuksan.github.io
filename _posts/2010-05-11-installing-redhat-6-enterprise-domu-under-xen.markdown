---
author: admin
comments: true
date: 2010-05-11 21:48:57+00:00
layout: post
slug: installing-redhat-6-enterprise-domu-under-xen
title: Installing RedHat 6 Enterprise DomU under Xen
wordpress_id: 185
categories:
- Linux
tags:
- Centos
- Centos6
- RHEL6
---

Recently I downloaded RedHat 6 Enteprise beta (RHEL6). I wanted to install it as a Xen guest (DomU) on top of an existing Centos 5 Xen host. Unfortunately it did not work out of the box. I ran

    
    virt-install --prompt


on the Xen host which let me install RHEL6 however when the install rebooted I was greeted with this error message

    
    fs = fsimage.open(file, get_fs_offset(file))
    IOError: [Errno 95] Operation not supported


Fortunately ﻿Karanbir Singh had a blog post about this at

[http://www.karan.org/blog/index.php/2010/04/28/rhel6-xen-domu-on-a-centos-5-dom0](http://www.karan.org/blog/index.php/2010/04/28/rhel6-xen-domu-on-a-centos-5-dom0#c5274)

Differences I found were that I had to make the root partition an ext2 filesystem as well. Also I found out that I couldn't review the partition layout if I ran the installation in the text mode. I had to use VNC to be able to set proper partition types. 
