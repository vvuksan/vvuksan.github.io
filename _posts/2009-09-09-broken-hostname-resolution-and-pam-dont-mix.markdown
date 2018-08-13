---
author: admin
comments: true
date: 2009-09-09 13:58:53+00:00
layout: post
slug: broken-hostname-resolution-and-pam-dont-mix
title: Broken hostname resolution and PAM don't mix
wordpress_id: 41
---

I don't mean PAM the cooking spray but Pluggable Authentication modules. I was asked to change some DNS settings for a set of hosts ie. move them from one domain to another e.g. from them being in domain.com to be in domain.net. At the end of the process head node all of the sudden started refusing logins with following error message

    
    fatal: Access denied for user vvuksan by PAM account configuration


It took some hair pulling but after a while I concluded that the headnodes hostname was set to the old name e.g. server5.domain.com which was no longer resolvable. As soon as hostname was changed ie.

    
    % hostname server5.domain.net


Things automagically started working again. Hope this prevents someone from going bald :-).
