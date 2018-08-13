---
author: admin
comments: false
date: 2014-02-07 21:04:11+00:00
layout: post
slug: building-your-own-binary-packages-for-cumulus-linux-powerpc
title: Building your own binary packages for Cumulus Linux (PowerPC)
wordpress_id: 606
categories:
- Networking
---

[Cumulus Networks](http://cumulusnetworks.com/)  is a new entrant in the network gear space. What separates them from other players is that they are not selling hardware but their own network focused Linux distribution called Cumulus Linux. Basically you buy a switch from one of their resellers or ODMs then pay Cumulus a yearly support license. There are a number of interesting things you can do like run your own code on the switch as well as use common Linux commands to configure the switch e.g. brctl, ports are exposed as Linux network interfaces etc.

One of the first things we ended up doing is installing [Ganglia agent](http://ganglia.info/) so that we can monitor what's going on on the switch. Cumulus switch we had was running a PowerPC based control plane so that made things a bit tricky since we couldn't use any of the amd64 built packages. One way to build PowerPC packages would be to get an old PowerPC based Mac and install Linux on it. Unfortunately that seemed like a lot of work and overkill. I realized we could just use [Qemu ](http://http://wiki.qemu.org/Main_Page)which is an Open Source machine emulator so I could run PowerPC machine on my own laptop :-). Quickest way to get up and running is as follows.

On Ubuntu you will need to install following packages

apt-get install qemu-system-ppc openbios-ppc qemu-utils

**Warning**: Under at least Ubuntu 13.10 openbios-ppc doesn't seem to work well. If you get a blank yellow screen after you start the install you will need to get openbios from other places e.g. [https://github.com/qemu/qemu/tree/master/pc-bios](https://github.com/qemu/qemu/tree/master/pc-bios)

Once you get those you will need to download Debian Squeeze for PowerPC. You will need to download



	
  * vmlinux

	
  * initrd.gz


from

[http://ftp.debian.org/debian/dists/squeeze/main/installer-powerpc/current/images/powerpc64/netboot/](http://ftp.debian.org/debian/dists/squeeze/main/installer-powerpc/current/images/powerpc64/netboot/)

as well as the netboot image e.g.

[http://cdimage.debian.org/cdimage/archive/6.0.8/powerpc/iso-cd/debian-6.0.8-powerpc-netinst.iso](http://cdimage.debian.org/cdimage/archive/6.0.8/powerpc/iso-cd/debian-6.0.8-powerpc-netinst.iso)

Reason why you need initrd.gz and vmlinux is that if you try to do an install straight off the CD-ROM your install will hang here

[![Power PC install QEMU](http://blog.vuksan.com/wp-content/uploads/2014/02/powerpc_install.png)](http://blog.vuksan.com/wp-content/uploads/2014/02/powerpc_install.png)

Once you have those pieces initiate the install with

    
    qemu-img create -f qcow2 squeeze-powerpc.img 10G
    sudo qemu-system-ppc -m 256 -kernel vmlinux \
     -cdrom debian-6.0.8-powerpc-netinst.iso \
              -initrd initrd.gz -hda squeeze-powerpc.img -boot d -append "root=/dev/ram" \
              -net nic,macaddr=00:16:3e:00:00:02 -net tap


Now follow the installation process as you would if you were installing Debian or Ubuntu from scratch. When you are done with the install shut down the emulator. Now to invoke your PowerPC emulator execute

    
    sudo qemu-system-ppc -m 256 -hda squeeze-powerpc.img  \
               -net nic,macaddr=00:16:3e:00:00:02 -net tap


Congratulations you are done. What you end up with is this

    
    root@debian:~# cat /proc/cpuinfo 
    processor    : 0
    cpu        : 740/750
    temperature     : 62-64 C (uncalibrated)
    revision    : 3.1 (pvr 0008 0301)
    bogomips    : 33.14
    timebase    : 16570400
    platform    : PowerMac
    model        : Power Macintosh
    machine        : Power Macintosh
    motherboard    : AAPL,PowerMac G3 MacRISC
    detected as    : 49 (PowerMac G3 (Silk))
    pmac flags    : 00000000
    pmac-generation    : OldWorld
    Memory        : 256 MB
