---
author: admin
comments: false
date: 2016-05-03 18:00:00+00:00
layout: post
slug: ganglia-webfrontend-ubuntu-1604-install-issue
title: Ganglia Web frontend in Ubuntu 16.04 install issue
tags:
- Ganglia
- Ubuntu
---

Ubuntu 16.04 Xenial comes with Ganglia Web Front end 3.6.1 included however doesn't pull in all the
dependencies. If you get an error like this

<pre>
Sorry, you do not have access to this resource. "); } try { $dwoo = new Dwoo($conf['dwoo_compiled_dir'], $conf['dwoo_cache_dir']); } catch (Exception $e) { print "
</pre>

You are missing Mod PHP and PHP7-XML module. To correct that you need to do execute following commands

    sudo apt-get install libapache2-mod-php7.0 php7.0-xml ; sudo /etc/init.d/apache2 restart

If you don't have Ganglia web frontend enabled all you need to do is type

    sudo ln -s /etc/ganglia-webfrontend/apache.conf /etc/apache2/sites-enabled/001-ganglia.conf
    sudo /etc/init.d/apache2 restart
