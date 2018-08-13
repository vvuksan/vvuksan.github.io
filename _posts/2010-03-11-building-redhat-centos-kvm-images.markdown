---
author: admin
comments: true
date: 2010-03-11 16:37:04+00:00
layout: post
slug: building-redhat-centos-kvm-images
title: Building Redhat/CentOS KVM images on Ubuntu 9.10
wordpress_id: 130
categories:
- Systems Management
---

This is a quick recipe on how to create a Redhat/CentOS KVM image on Ubuntu 9.10 (karmic). First make sure you have Virtualization (VT) turned on. For example Dell laptops will have it disabled by default. Go into BIOS and enable it. To check whether it is turned on run

    
    egrep '(vmx|svm)' /proc/cpuinfo


If this comes out empty VT is not enabled and KVM will not work.

Install kvm packages

    
    sudo apt-get install qemu-kvm


Edit /etc/qemu-ifup to add **virbr0** as the bridge to which KVM guest should attach itself. Comment out line below and add lines below e.g.

    
    #/usr/sbin/brctl addif ${switch} $1
    /usr/sbin/brctl addif virbr0 $1


Same change needs to be done in /etc/qemu-ifdown ie.

    
    #/usr/sbin/brctl delif ${switch} $1
    /usr/sbin/brctl delif virbr0 $1


Download CentOS 5.4 Boot ISO image e.g.

    
    wget http://www.gtlib.gatech.edu/pub/centos/5.4/isos/x86_64/CentOS-5.4-x86_64-netinstall.iso


Create an empty image (last argument is the image size)

    
    kvm-img create -f qcow2 centos5.img 10G


Launch install (-m is memory size)

    
    sudo kvm -hda centos5.img -cdrom boot.iso -m 512 -boot d \
           -net nic,vlan=0,model=e1000,macaddr=00:16:3e:de:00:01 -net tap


Install CentOS however you like. When you are done your CentOS install will reboot and try to boot off the CD-ROM. At this point shut down the KVM guest by closing the window. To run it remove the cdrom references and boot option e.g.

    
    sudo kvm -hda centos5.img -m 512 \
           -net nic,vlan=0,model=e1000,macaddr=00:16:3e:de:00:01 -net tap


Note: I am setting a fixed MAC address. You can leave it off and it will be generated randomly every time you start up kvm instance.
