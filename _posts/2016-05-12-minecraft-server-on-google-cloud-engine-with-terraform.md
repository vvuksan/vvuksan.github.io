---
author: admin
comments: false
date: 2016-05-11 12:00:00+00:00
layout: post
slug: minecraft-server-on-google-cloud-engine-with-terraform
title: Setup Minecraft Server on Google Cloud Engine with terraform
tags:
- Google Cloud Engine
- Gaming
- Minecraft
- terraform
---

My children like to play Minecraft and they often like to play with their friends and cousins who are remote. To do so in the past I would set up my laptop at the house, set up port forwarding on the
router, etc. This would often not work as the router would not accept the changes, my laptop firewall was on etc. Instead I decided to shift all this to the cloud.
In this particular example I will be using Google Cloud Engine since it allows you to have persistent disks. To minimize costs I will automate creation and destruction of minecraft server(s) using Hashicorp's [Terraform](https://terraform.io).

All the terraform template and files can be found in this specific Github Repo

[https://github.com/vvuksan/terraform-playground](https://github.com/vvuksan/terraform-playground/tree/master/minecraft-server/google_cloud)

You will need to sign up for a Google Cloud account. You may also optionally buy a domain name from a registrar so that you don't need
to enter IP addresses in your minecraft client. If you do so rename dns.tf.disabled to dns.tf and change this section

<pre>
variable "domain_name" {
  description = "Domain Name"
  default     = "change_to_the_domain_name_you_bought.xyz"
}
</pre>

As described in the README what this set of templates will do is create a persistent disk where you will store your gameplay and spin up
a minecraft server just for that time being. When you want to play
you will need to type

<pre>
make create
</pre>

and when you are done playing you will type

<pre>
make destroy
</pre>

Cost of this should be minimal. In the TF template I'm setting a persistent disk of size of 10 GB (change that in main.tf if you need to). That will cost you approximately $0.40 per month. On top of it you'd be paying for
g1.small instance cost which is about $0.02 per hour. You can certainly opt for a faster instance by adjusting the instance size in main.tf file.
Also if you are using DNS there will be DNS query costs but those should be minimal.

Have fun.
