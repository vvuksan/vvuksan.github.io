---
author: admin
comments: false
date: 2014-01-10 21:05:19+00:00
layout: post
slug: running-arista-veos-under-linux-kvm
title: Running Arista vEOS under Linux KVM
wordpress_id: 602
categories:
- Networking
tags:
- Arista
- networking
---

At my current job we run a lot of [Arista](http://www.ar) gear. They are great little boxes. You can also run Ganglia on them :-) since they are basically Fedora Core 14 OS with some Arista proprietary sauce. You can find Arista specific Ganglia gmetric scripts here

[https://github.com/ganglia/gmetric/tree/master/arista](https://github.com/ganglia/gmetric/tree/master/arista)

On occasion I have wanted to test some things and Arista offers VM images you can run on your choice of virtualization. You can find more details here

[https://eos.aristanetworks.com/2011/11/running-eos-in-a-vm/](https://eos.aristanetworks.com/2011/11/running-eos-in-a-vm/)

I use KVM on my Ubuntu laptop and although booting the imaged worked I could not SSH into vEOS from my laptop. After a bit of testing I discovered that Arista's document misses a very important option ie.

-net tap

So full invocation is really




    
    kvm -cdrom Aboot-veos-2.0.8.iso -boot d -hda EOS-4.12.3-veos.vmdk -usb -m 1024 \
        -net nic,macaddr=52:54:00:01:02:03,<wbr></wbr>model=e1000 \
        -net nic,macaddr=52:54:00:01:02:04,<wbr></wbr>model=e1000 \
        -net nic,macaddr=52:54:00:01:02:05,<wbr></wbr>model=e1000 \
        -net nic,macaddr=52:54:00:01:02:06,<wbr></wbr>model=e1000 \
        -net nic,macaddr=52:54:00:01:02:07,<wbr></wbr>model=e1000 \
        -net tap







After that I was able to configure vlan 1 to e.g. 192.168.122.2[
](http://192.168.122.2/24)




Log in into the console you just fired up and type



    
    localhost#configure 
    localhost(config)#interface vlan 1
    localhost(config-if-Vl1)#ip address 192.168.122.2/24
    localhost(config)#username admin secret 0 secret


You also want to set the password e.g. here I set it to secret and voila you can now SSH into 192.168.122.2. If you have too many SSH private keys loaded log in may not work so turn of public key authentication e.g.

    
    ssh -o PubkeyAuthentication=no admin@192.168.122.2




Only note may be that if you just install libvirt /etc/qemu-ifup doesn't quite work since it determines which bridge to connect to based on the default route To "fix" that add






    
    switch="virbr0"




Just above this section in /etc/qemu-ifup



    
    # only add the interface to default-route bridge if we
    # have such interface (with default route) and if that
    # only add the interface to default-route bridge if we
    # have such interface (with default route) and if that
    # interface is actually a bridge.
    # It is possible to have several default routes too
    for br in $switch; do
        if [ -d /sys/class/net/$br/bridge/. ]; then
            if [ -n "$ip" ]; then
              ip link set "$1" master "$br"
            else
              brctl addif $br "$1"
            fi
            exit    # exit with status of the previous command
        fi
    done






