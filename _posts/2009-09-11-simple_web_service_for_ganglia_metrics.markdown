---
author: admin
comments: true
date: 2009-09-11 20:58:06+00:00
layout: post
slug: simple_web_service_for_ganglia_metrics
title: Simple "web service" for Ganglia metrics
wordpress_id: 44
---

Here is a simple PHP script to allow you to get current Ganglia metrics. You will need Ganglia web installation. Drop this script somewhere. Then invoke it via e.g.

http://mygangliaserver/ganglia/metric.php?server=web1&metric_name=load_one

Where server is the name of the server for which you want metrics and metric_name is the exact name of the metric you are looking for e.g. load_one, disk_free etc. Only thing that is returned is either ERROR message or actual value.

    
    <?php
    
    $GANGLIA_WEB="/var/www/html/ganglia";
    
    include_once "$GANGLIA_WEB/conf.php";
    include_once "$GANGLIA_WEB/get_context.php";
    # Set up for cluster summary
    $context = "cluster";
    include_once "$GANGLIA_WEB/functions.php";
    include_once "$GANGLIA_WEB/ganglia.php";
    include_once "$GANGLIA_WEB/get_ganglia.php";
    
    # Get a list of all hosts
    $ganglia_hosts_array = array_keys($metrics);
    
    $found = 0;
    
    # Find a FQDN of a supplied server name.
    for ( $i = 0 ; $i < sizeof($ganglia_hosts_array) ; $i++ ) {
     if ( strpos(  $ganglia_hosts_array[$i], $_GET['server'] ) !== false  ) {
     $fqdn = $ganglia_hosts_array[$i];
     $found = 1;
     break;
     }
    }
    
    if ( $found == 1 ) {
     if ( isset($metrics[$fqdn][$_GET['metric_name']]['VAL']) ) {
     echo($metrics[$fqdn][$_GET['metric_name']]['VAL']);
     } else {
     echo("ERROR: Metric value not found");
     }
    } else {
     echo "ERROR: Host not found";
    }
    
    ?>


Nothing fancy. It contains rudimentary error checking so please be gentle :-). Feel free to extend it satisfy your needs. Also this is likely not scalable if you have hundreds of hosts and tons of requests.
