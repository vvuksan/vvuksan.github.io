---
author: admin
comments: true
date: 2010-08-20 12:29:20+00:00
layout: post
slug: tunnel-all-your-traffic-on-hostile-networks-with-openvpn
title: Tunnel all your traffic on "hostile" networks with OpenVPN
wordpress_id: 341
categories:
- Networking
- Systems Management
---

I am often on wireless networks that are unsecured ie. either don't use encryption or if they are I may not trust they will not tamper with my data (you never know). To protect my traffic on such networks I decided to tunnel nearly all my traffic through an OpenVPN server while I'm on such networks. I will show you how you can do it yourself on your Linux or Mac laptops. You should be able to do similar in Windows but it may be a bit more work on the client.


## OpenVPN server setup




Set up OpenVPN on a network you trust e.g. home, work, cloud etc. You can either use Community Edition of OpenVPN which is free [http://openvpn.net/index.php/open-source/downloads.html](http://openvpn.net/index.php/open-source/downloads.html) or you may want to pay OpenVPN money for their OpenVPN appliance package. I prefer using [pfSense](http://pfsense.com/) which is customized FreeBSD distribution geared for firewalls/routers with superb Web GUI. If you are gonna use the Community Edition follow the [Quickstart guide](http://openvpn.net/index.php/open-source/documentation/howto.html#quick).




One last step is to make sure that VPN network ie. 10.8.0.0/16 is NATed e.g. on a Linux OpenVPN server you could do






    
    <code style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: #eeeeee; font-family: Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, serif; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;"><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">iptables </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">-</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">t nat </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">-</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">A POSTROUTING </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">-</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">s </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln"><span style="color: #4e0000;">10.8.0.0</span></span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">/</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun"><span style="color: #4e0000;">16</span></span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln"> </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">-</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">o eth0 </span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #888888; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pun">-</span><span style="font-size: 14px; vertical-align: baseline; background-image: initial; background-attachment: initial; background-origin: initial; background-clip: initial; background-color: transparent; color: #333333; background-position: initial initial; background-repeat: initial initial; padding: 0px; margin: 0px; border: 0px initial initial;" class="pln">j MASQUERADE</span></code>







## OpenVPN client setup




Configure OpenVPN client to connect to your OpenVPN server. You can find the [client HOWTO](http://openvpn.net/index.php/openvpn-client/howto-openvpn-client.html) here.




Make sure you can access your home/work network. This will in general provide you with "split-tunnel" access ie. only traffic intended for your home/work network will be tunneled through VPN and everything else will go the normal "insecure" way.




## Tunnel all traffic


**Update**: Shame on me. Someone has already posted the directions on how to do this at

[http://manoftoday.wordpress.com/2006/12/03/openvpn-20-howto/](http://manoftoday.wordpress.com/2006/12/03/openvpn-20-howto/)

Thanks to [@somic](http://twitter.com/somic) for pointing this out.


Tricky part in all this is that OpenVPN uses a simple TUN/TAP interface through which tunnels all the traffic. Temptation is to simply add an entry in the OpenVPN file that sets a default route through OpenVPN. This will likely fail as you will now have competing default routes. Instead what you need to do is add a route to your VPN server that uses the wireless networks default gateway and make your VPN interface the default route. This way all the traffic goes into the VPN interface and OpenVPN takes care of tunneling it through.




For this you will need to configure an external script that fires off once VPN tunnel is up. To enable post-up script put following two lines in your ovpn file



    
    <span style="text-decoration: line-through;">script-security 3 system
    up /usr/local/bin/set_up_routes.sh</span>


Your set_up_routes.sh would look something like this. Please change the VPN_SERVER_IP variable to the IP of your OpenVPN server.

    
    <span style="text-decoration: line-through;">#!/bin/sh
    # Note the wireless network default gateway
    DEFAULT_GATEWAY=`netstat -nr | grep ^0.0.0.0 | awk '{ print $2 }'`
    # Find out what's the IP on the
    VPN_GATEWAY=`netstat -nr | grep tun | grep -v 0.0.0.0 | awk '{ print $2 }' | sort | uniq`
    VPN_GATEWAY=`ifconfig  | grep 172.16 | cut -f3 -d:  | cut -f1 -d" "`
    VPN_SERVER_IP="1.2.3.4"
    sudo /sbin/route del default
    #
    sudo /sbin/route add -host $VPN_SERVER_IP gw $DEFAULT_GATEWAY
    # Don't tunnel traffic to 2.3.4.5 since it's already SSLized
    sudo /sbin/route add -host 2.3.4.5 gw $DEFAULT_GATEWAY
    sudo /sbin/route add default gw $VPN_GATEWAY</span>


This script was tested under Ubuntu Linux but should work the same under Mac OS X. On Windows you may need to use PowerShell or use Cygwin.


## Tunneling traffic for specific IPs


If you only wish to tunnel traffic for particular set of IPs you only need to add those routes to your ovpn file e.g.

    
    route 72.0.0.0 255.0.0.0
    route 75.0.0.0 255.0.0.0




You do NOT need to go through the excercise of setting up a script.




If you are looking for other OpenVPN guides [Sam Johnston](https://twitter.com/samj) has a OpenVPN guide on howto set up OpenVPN in a VPS


[http://samj.net/2010/01/howto-set-up-openvpn-in-vps.html](http://samj.net/2010/01/howto-set-up-openvpn-in-vps.html)
