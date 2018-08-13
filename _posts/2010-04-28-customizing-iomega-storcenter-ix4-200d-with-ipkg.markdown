---
author: admin
comments: true
date: 2010-04-28 14:16:25+00:00
layout: post
slug: customizing-iomega-storcenter-ix4-200d-with-ipkg
title: Customizing iomega StorCenter ix4-200d with ipkg
wordpress_id: 179
categories:
- Linux
tags:
- Linux
---

I have the iomega StorCenter ix4-200d. It is a nice little NAS with a number of decent features including rsync server etc. Unfortunately there were couple things I wanted fixed since for example rsync was at version 2.6.9 which does not support incremental updates. Machine runs a custom Linux distribution so I figured someone must have figured out how to customize it. I found part of the answer here

[www.krausam.de/?p=33](http://www.krausam.de/?p=33)

To enable SSH you need to log in as administrator to your StorCenter then go to https://<storcenterIP>/support.html. Turn on SSH access. StorCenter will reboot. Then you will be able to ssh into the box as root where password is your admin password with soho prepended ie. if your web gui password is secret then root password is sohosecret.

Post has a way to bootstrap Debian on the box however I found an easier solution ie. StorCenter ships with ipkg utility which is similar to apt-get and yum commands. To enable proper repositories I searched and found them here

[http://forum.synology.com/enu/viewtopic.php?f=40&t=5823](http://forum.synology.com/enu/viewtopic.php?f=40&t=5823)

Easy way to add them is cut and paste following

    
    cat <<EOF > /etc/ipkg.conf
    src cross http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/unstable
    src native http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/native/unstable
    EOF


Then type

    
    ipkg update


After that you can check the list of available packages by typing

    
    ipkg list | less


To install packages type

    
    ipkg install <package_name>


Please note that packages are installed in /opt so adjust paths properly ie. screen is installed in

/opt/bin/screen

Hope this helps someone
