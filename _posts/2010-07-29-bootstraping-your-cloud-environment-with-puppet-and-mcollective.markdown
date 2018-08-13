---
author: admin
comments: true
date: 2010-07-29 01:31:49+00:00
layout: post
slug: bootstraping-your-cloud-environment-with-puppet-and-mcollective
title: Bootstraping your cloud environment with puppet and mcollective
wordpress_id: 298
categories:
- Cloud Computing
- Linux
tags:
- Cloud provisioning
- Disaster Recovery
- mcollective
---

This is a "recipe" on how to bootstrap your whole environment in case of a disaster ie. your data center goes dark or if you are migrating from one environment to another. This guide differs from others in that it uses mcollective and DNS to provide you with greater flexibility in deploying and bootstraping environments. Some of the alternate ways are [ec2-boot-init by R.I. Pienaar](http://github.com/ripienaar/ec2-boot-init#readme) or Grig Gheorghiu's [Bootstrapping EC2 images as Puppet clients](http://agiletesting.blogspot.com/2009/09/bootstrapping-ec2-images-as-puppet.html).


## Intro


You will need two disk images, your code repository and your DB backup and you can rebuild your whole environment from scratch in a relatively short period of time. This could be adapted to generic cloud provisioning however use case I'm trying to address is disaster recovery. We are using DNS so that we can keep hostnames consistent between environments ie. mail01 will be a mail server in all environments instead of domU-1-2-3-4 in one, rack-2345 in other etc.


## Set up a master node image


Master node is the node that controls all the other nodes. Most importantly it contains all your configuration management data. You will need to install following



	
  * mcollective with ActiveMQ

	
  * DnsMasq

	
  * Puppet from [Puppet Labs](http://www.puppetlabs.com/)


1.  You will need to get a DNS name from a dynamic DNS provider such as DynDNS. Once you have that you will need to write a shell script that runs at boot and sets your EC2 private IP to that DNS name. Let's say we want our controller station to be known as controller.ec2.domain.com we can do something like this

    
    IP=`facter ipaddress`
    change_my_dns_ip controller.ec2.domain.com
    # Delete any entries from hosts
    sed -i "/controller.ec2.domain.com/d" /etc/hosts
    echo "${IP}     controller.ec2.domain.com" >> /etc/hosts


2. Set up ActiveMQ to be used with mcollective [http://code.google.com/p/mcollective/wiki/GettingStarted](http://code.google.com/p/mcollective/wiki/GettingStarted)
3. Set up mcollective

Configure controller.ec2.domain.com as the stomp host in your mcollective configuration for both client and server configuration.

4.Install dnsmasq. You don't need to configure anything since by default dnsmasq will read /etc/hosts and serve those names over DNS

5. Install puppetmaster, configure it anyway you want

6. Image it


## Set up a generic/worker node image


You will need to Install following



	
  * Mcollective

	
  * puppet agent


1. On the worker node you need to configure the server piece of mcollective and make sure the stomp.host is pointed to the master ie.  controller.ec2.domain.com.

2. Create a reboot agent (we'll discuss later how to use it). Please visit [http://code.google.com/p/mcollective/wiki/SimpleRPCIntroduction](http://code.google.com/p/mcollective/wiki/SimpleRPCIntroduction) for an example. Create a new file ie. reboot.rb. Paste this code in it

    
    module MCollective
     module Agent
      class Reboot<RPC::Agent
        def reboot_action
         `/sbin/shutdown -r now`
        end
      end
     end
    end


Copy the resulting file to the mcollective agents directory

3. Add following script to the bootup

    
    MASTER=`host controller.ec2.domain.com | grep address | cut -f4 -d" "`
    IS_ALREADY_SET=`grep -c ec2.domain.com /etc/resolv.conf`
    if [ $IS_ALREADY_SET -lt 1 ]; then   
    sed -i "s/^search .*/search ec2.domain.com/g" /etc/resolv.conf
    sed -i "s/^nameserver/nameserver ${MASTER}\nnameserver/g" /etc/resolv.conf
    fi
    # Set Hostname
    IP=`facter ipaddress`
    MY_HOST=`/bin/ipcalc --silent --hostname ${IP} | cut -f2 -d=`
    hostname ${MY_HOST}


What that does is point tells your worker nodes to use controller DNS for resolving names as well as setting your hostname.

4. Get the mcollective puppet plugin from [github](http://github.com/ripienaar/mcollective-plugins/tree/master/agent/puppetd/)

5. Image it


## Bringing up the environment


You will need to start the master instance first since that's the instance that everyone will be talking to. As soon as it's up you can start up as many instances as you'd like.

While you wait rsync your puppet manifests and configurations to the master node

To find out what nodes are up and available issue mc-ping from the master and you should get a response similar to this

    
    # mc-ping
    controller.ec2.domain.com               time=77.21 ms
    domu-12-31-55-11-22-18.compute-1.internal time=188.76 ms


Trouble is that hostnames on the worker nodes are set to Amazon names. We want to make them recognizable e.g. mail01.

To do so simply add the IP of the worker instance and it's name into /etc/hosts on the master e.g.

    
    echo "10.1.2.3      mail01.ec2.domain.com" >> /etc/hosts


Reload dnsmasq configuration ie.

    
    /etc/init.d/dnsmasq reload


What this has bought you is reverse DNS resolution of the node.  To take effect you will need to reboot the worker node. We already have the reboot agent on the worker nodes so all we have to do is run following command on the master node

    
    ./mc-rpc -F hostname=domu-12-31-55-11-22-18 reboot reboot


This will seek out the domU-1-2-3-4 host and reboot it (--arg is irrelevant so put anything). Once the machine is up it will advertise it's new name :-) ie. running mc-ping will show you this

    
    # mc-ping
    controller.ec2.domain.com           time=47.59 ms
    mail01.ec2.domain.com               time=80.71 ms


Now let's activate puppet. From master node run

    
    # mc-puppetd -F hostname=mail01 runonce
    
     * [ ============================================================> ] 1 / 1
    
    Finished processing 1 / 1 hosts in 1051.23 ms


Once that is done puppetca should give you this

    
    
    
    
    # puppetca --list
    mail01.ec2.domain.com





Sign it

    
    # puppetca –sign mail01.ec2.domain.com


Now you can simply run

    
    # mc-puppetd -F hostname=mail01 enable


and off you go. Now lather, rinse, repeat to get the rest of the instances going. You would certainly want to automate this further but I leave that exercise to you :-).

If you are looking for an easy cross-cloud API check out my "[Provision to cloud in 5 minutes using fog](http://blog.vuksan.com/2010/07/20/provision-to-cloud-in-5-minutes-using-fog/)".
