---
author: admin
comments: false
date: 2019-10-24 18:00:00+00:00
layout: post
slug: ubuntu-19-10-chromium
title: Ubuntu 19.10 Chromium issues after 19.04 upgrade 
tags:
- Ubuntu 19.10
- Ubuntu 19.04
- Snap
- Chromium
- Password Manager Service
---

Recently I upgraded from Ubuntu 19.04 to 19.10. Upgrade was uneventful except for Chromium losing all my
saved passwords and personal HTTPS certificates. Main cause of the issue is the new chromium packaging. As of 19.10
chromium is utilizing snap packaging instead of deb. You can read the rational behind the change 
[on the Ubuntu blog](https://ubuntu.com/blog/chromium-in-ubuntu-deb-to-snap-transition). 

As a result on first invocation `$HOME/.config/chromium` is copied into `$HOME/snap/chromium/.config/chromium`. 
Unfortunately that is not sufficient as you end up with a following error message

<pre>
[23083:23264:1024/150717.374528:ERROR:token_service_table.cc(140)] Failed to decrypt token for service AccountId-19854958475897323
</pre>

Solution is to run 

<code>
snap connect chromium:password-manager-service
</code>

Thanks to [this post for providing the solution.](https://discourse.ubuntu.com/t/call-for-testing-chromium-browser-deb-to-snap-transition/11179/164).

As far as personal HTTPS certificates are concerned your best course of action is to either export those prior to the
upgrade or if you don't have that option [download latest Chromium build](https://www.chromium.org/getting-involved/download-chromium) then export the cert and reimport into your new shiny chromium :-).

If you are curious what Chromium is using for it's config directory you can enter a following URL

<pre>
chrome://version
</pre>
