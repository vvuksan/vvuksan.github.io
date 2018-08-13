---
author: admin
comments: true
date: 2009-08-27 15:43:08+00:00
layout: post
slug: howto-install-a-ssl-certificate-with-intermediate-certificate-on-a-cisco-load-balancer
title: Howto install a SSL certificate with intermediate certificate on a Cisco load
  balancer
wordpress_id: 21
categories:
- Networking
---

This is a common problem across many different platforms. You generate a CSR, get a certificate but forget or don't realize that besides installing the signed certificate you need to install the CA (Certificate Authority) Intermediate certificate. As a result some of the older browsers may complain about an invalid certificate or Java code will fail with following error message


Exception in thread "main" javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path validation failed: java.security.cert.CertPathValidatorException: CA key usage check failed: keyCertSign bit is not set



Solution is to download the intermediate certificate from the CA e.g. Verisign and GoDaddy and include it with the certificate. For instance in Apache you need to include **SSLCertificateChainFile** directive with the path to the intermediate certificate. On Cisco loadbalancer you would need to use [following Cisco document](http://docwiki.cisco.com/wiki/SSL_Termination_on_the_Cisco_Application_Control_Engine_Using_an_Existing_Chained_Certificate_and_Key_in_Routed_Mode_Configuration_Example). Specifically this directive

    
    ACE-1/routed(config)# crypto chaingroup intermed-1
    ACE-1/routed(config-chaingroup)# cert intermediate.pem




The chaingroup needs to be applied to the ssl-proxy service in addition to the already configured certificate and key.




    
    ACE-1/routed(config)# ssl-proxy service proxy-1
    ACE-1/routed(config-ssl-proxy)# chaingroup intermed-1


If you got your certificate from Verisign you can check whether you installed it properly here

[https://knowledge.verisign.com/support/ssl-certificates-support/index?page=content&actp=CROSSLINK&id=ar1130](https://knowledge.verisign.com/support/ssl-certificates-support/index?page=content&actp=CROSSLINK&id=ar1130)

You can always of course misconfigure things causing lots of time to be wasted. For instance on one occasion a well known managed hosting provider that was in charge of configuring the Cisco load balancer configured the load balancer as follows

    
    crypto chaingroup some.domain.com
       cert some.domain.com.cert
       cert intermediate.cert
    ssl-proxy service vip-1.2.3.4-ps
       key some.domain-com.key
       cert some.domain.com.cert
       chaingroup some.domain.com
       ssl advanced-options ssl-parameter-map-1


This is incorrect as the server certificate SHOULD NOT be included in the intermediate certificate chain. Otherwise the helpful Verisign test applet will complain with following message.


Two certificates were found with the same common name. The certificate installation checker cannot determine which is the correct certificate for the Web server. Remove the incorrect certificate and then test again.



Most browsers will work correctly however Java code will exhibit errors from the top of the article. Solution for the above problem is this

    
    crypto chaingroup verisign-intermediate
      cert intermediate.cert


Then included that chaingroup in the ssl-proxy directive. Once that was done the issue went away. Hope this saves someone some debugging time.
