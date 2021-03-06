---
author: admin
comments: false
date: 2012-09-28 19:55:25+00:00
layout: post
slug: monitoring-health-of-delllsi-raid-arrays-with-ganglia
title: Monitoring health of Dell/LSI RAID arrays with Ganglia
wordpress_id: 562
categories:
- Monitoring
---

I have couple hundred Dell systems with LSI RAID arrays however we lacked hardware monitoring which would occasionally result in situations where there would be multiple disk failures that would not get caught. Some time ago I read this post about using MegaCli to monitor Dell's RAID controller

[http://timjacobs.blogspot.com/2008/05/installing-lsi-logic-raid-monitoring.html](http://timjacobs.blogspot.com/2008/05/installing-lsi-logic-raid-monitoring.html)

One thing I did not like about this approach is that it may generate too many e-mails as it will send e-mails every hour until the disk has been fixed. Instead I used Ganglia with Nagios to provide me with similar type of functionality.

Create a file called analysis.awk with following content

    
        /Device Id/ { counter += 1; device[counter] = $3 }
        /Firmware state/ { state_drive[counter] = $3 }
        /Inquiry/ { name_drive[counter] = $3 " " $4 " " $5 " " $6 }
        END {
        for (i=1; i<=counter; i+=1) printf ( "Device %02d (%s) status is: %s\n", device[i], name_drive[i], state_drive[i]); 
        }


Get MegaCli utilities from e.g. [RPMFind](http://rpmfind.net/linux/rpm2html/search.php?query=megacli). Copy following BASH script into a file and run into from a cron at frequency you need e.g. every 30 minutes, hour etc.

    
    #!/bin/sh
    
    GMETRIC_BIN="/usr/bin/gmetric -d 7200 " 
    
    MEGACLI_DIR="/opt/MegaRAID"
    
    MEGACLI_PATH="/opt/MegaRAID/MegaCli/MegaCli64"
    
    BAD_DISKS=`$MEGACLI_PATH -PDList -aALL | awk -f ${MEGACLI_DIR}/analysis.awk | grep -Ev "*: Online" | wc -l` 
    
    if [ $BAD_DISKS -eq 0 ]; then
        STATUS="All RAID Arrays healthy"
    else
        STATUS=`$MEGACLI_PATH -PDList -aALL | awk -f ${MEGACLI_DIR}/analysis.awk | grep -Ev "*: Online"` 
    fi
    
    $GMETRIC_BIN -t uint16 -n failed_unconfigured_disks -v $BAD_DISKS -u disks
    $GMETRIC_BIN -t string -n raid_array_status -T "Raid Array Status" -v "$STATUS"




This will create two different Ganglia metrics. One is number of failed or unconfigured disks and another one is just a string value that gives you details on the failure e.g. Disk 4 failed. Besides being able to alert on this metric it also gives me a valuable data point I can use to correlate node behavior ie. when disk failed load on the machine went up.

If you are using [Ganglia with Nagios integration](http://sourceforge.net/apps/trac/ganglia/wiki/ganglia_nagios_integration) you have two different options on how you want to alert.

1. Create a separate check for every host you want monitored e.g.

    
    define command{
            command_name    check_ganglia_metric
            command_line    /bin/sh /var/www/html/ganglia/check_ganglia_metric.sh host=$HOSTADDRESS$ metric_name=$ARG1$ operator=$ARG2$ critical_value=$ARG3$
            }
    
    define service{
            use                   generic-service
            host_name             server1
            service_description   Failed/unconfigured RAID disk(s)
            check_command         check_ganglia_metric!failed_unconfigured_disks!more!0.1
        }




2. Create a single check that gives you a single alert if at least one machine has a bad disk (how I do it :-)). For this purpose I'm utilizing the check_host_regex which allows me to specify a regular expression of matching hosts. In my case I check every single host. If a host doesn't have the failed_disks metric I assume it doesn't have it and I "ignore" matches. My config is similar to this

    
    define command{
            command_name    check_host_regex_ignore_unknowns
            command_line    /bin/sh /etc/icinga/objects/check_host_regex.sh hreg=$ARG1$ checks=$ARG2$ ignore_unknowns=1
            }
    
    define service{
            use                             generic-service
            host_name                       server2
            service_description             Failed disk - RAID array
            check_command                   check_host_regex_ignore_unknowns!'.*'!failed_disks,more,0.5
        }
    
    


Which will give you something like this

# Services OK = 236, CRIT/UNK = 2 : CRITICAL compute-4566.domain.com failed_disks = 1 disks, CRITICAL git-0341.domain.com failed_disks = 1 disks


