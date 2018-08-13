---
author: admin
comments: true
date: 2011-11-23 21:16:47+00:00
layout: post
slug: operating-on-dell-raid-arrays-cheatsheet
title: Operating on Dell RAID arrays cheatsheet
wordpress_id: 487
---

I have to infrequently add new drives to Dell RAID arrays like H700. For some reason it takes me couple searches to find the info so here so I can find it later.

**List all drives**

    
    /opt/MegaRAID/MegaCli/MegaCli64 -PDList -aALL


**Create a RAID array (e.g. RAID 0)**

    
    /opt/MegaRAID/MegaCli/MegaCli64 -CfgLdAdd -r0 [32:4, 32:5] -aALL


**List working RAID arrays**

    
    /opt/MegaRAID/MegaCli/MegaCli64 -LDInfo -Lall -aALL


Confirm you got the right RAID array e.g. Virtual Disk 1

    
    /opt/MegaRAID/MegaCli/MegaCli64 -LDInfo -L1 -aALL


**Delete RAID array**

    
    /opt/MegaRAID/MegaCli/MegaCli64 -CfgLdDel -L1 -aALL



