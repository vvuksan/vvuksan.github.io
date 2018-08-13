---
author: admin
comments: false
date: 2016-05-10 12:00:00+00:00
layout: post
slug: rsyslog-server-tls-termination
title: Rsyslog server TLS termination
tags:
- Rsyslog
- Ubuntu
---

I was working with a customer trying to configure [Fastly's Log Streaming](https://docs.fastly.com/guides/streaming-logs/setting-up-remote-log-streaming)
and ship logs to their Rsyslog server. Fastly supports sending Syslog over TLS however it appeared that TLS handshake was not succeeding as we 
would end up with gibberish in the logs e.g. 

<pre>
May  3 13:22:08 192.168.0.10 #001#000#000M#033#000#020#023#000#001#000#000#016log.domain.com#000#002#000#005#001#000#000#000#000 
</pre>

I looked over a number of different guides with no luck. After trying a number of different things I ended up with a following configuration. This was
tested on RSyslog 7 and 8.

<pre>
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/openandclick.log
kern.*                          -/var/log/kern.log
mail.*                          -/var/log/mail.log

#
# Emergencies are sent to everybody logged in.
#
*.emerg                                :omusrmsg:*

# Setup disk assisted queues
$WorkDirectory /var/log/spool # where to place spool files
$ActionQueueFileName fwdRule1     # unique name prefix for spool files
$ActionQueueMaxDiskSpace 1g       # 1gb space limit (use as much as possible)
$ActionQueueSaveOnShutdown on     # save messages to disk on shutdown
$ActionQueueType LinkedList       # run asynchronously
$ActionResumeRetryCount -1        # infinite retries if host is down

#RsyslogGnuTLS
# CA certificate store. Uses generic Debian/Ubuntu CA store
$DefaultNetstreamDriverCAFile /etc/ssl/certs/ca-certificates.crt
$DefaultNetstreamDriverCertFile /etc/letsencrypt/archive/log.domain.com/fullchain1.pem
$DefaultNetstreamDriverKeyFile /etc/letsencrypt/archive/log.domain.com/privkey1.pem
$DefaultNetstreamDriver gtls

module(load="imtcp"
streamdriver.mode="1"
streamdriver.authmode="anon")
input(type="imtcp" port="5144" name="tcp-tls")
</pre>

It will use the TLS certificate from /etc/letsencrypt and listen to TLS requests on port 5144. There is no client
authentication ie. authmode=anon. If you want to authenticate clients you will need to change authmode to e.g.

<pre>
streamdriver.authMode="name" 
streamdriver.permittedpeer=["test1.example.net", "test.example.net"]
</pre>