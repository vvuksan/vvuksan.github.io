---
author: admin
comments: false
date: 2016-04-15 18:00:00+00:00
layout: post
slug: signing-aws-lambda-api-calls-with-varnish
title: Signing AWS Lambda API calls with Varnish
tags:
- Cloud
- AWS
- Varnish
- Lambda
---

A number of months ago Stephan Seidt [@evilhackerdude](https://twitter.com/evilhackerdude) posed
a question on Twitter if it was [possible to 
use Fastly to sign requests going to AWS Lambda](https://twitter.com/evilhackerdude/status/667315959242162176). 
For those who do not know what AWS Lambda is here is [Wikipedia's succinct explanation](https://en.wikipedia.org/wiki/Amazon_Lambda)

> AWS Lambda is a compute service that runs code in response to events and 
> automatically manages the compute resources required by that code. The purpose 
> of Lambda, as opposed to AWS EC2, is to simplify building smaller, on-demand 
> applications that are responsive to events and new information. AWS targets 
> starting a Lambda instance within milliseconds of an event.
>
> AWS Lambda was designed for use cases such as image upload, responding to 
> website clicks or reacting to output from a connected device. AWS Lambda 
>  can also be used to automatically provision back-end services triggered by custom requests.
>
> Unlike Amazon EC2, which is priced by the hour, AWS Lambda is metered in increments of 100 milliseconds.

Initially I thought this was not going to be possible since I thought I could only make asynchronous
calls however Stephan pointed out that there was a way to invoke synchronous calls as well since that is what 
[AWS API Gateway](https://aws.amazon.com/api-gateway/) does to expose Lambda functions.

In order to be able to send requests to Lambda you would need to sign requests going to Lambda. AWS has gone through a number of versions of their
signing API however for most services today you will need to use 
[signature version 4](http://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html). 
SIGV4 API relies on a number of HMAC and hashing functions that are not in stock varnish but are available in
the [Libvmod-Digest VMOD](https://github.com/varnish/libvmod-digest) if you are deploying your VCL on Fastly this 
VMOD is already built it.

### Code

You can find full VCL for signing requests to Lambda here

[https://github.com/vvuksan/misc-stuff/blob/master/lambda/lambda.vcl](https://github.com/vvuksan/misc-stuff/blob/master/lambda/lambda.vcl)

This code has some Fastly specific macros and functions which you can upload as custom VCL however most of the heavy lifting is done inside the aws4_lambda_sign_request subroutine so if you are using stock varnish copy that. Things to change in the vcl_recv are

<pre>
set req.http.access_key = "CHANGEME";
set req.http.secret_key = "CHANGEME";
</pre>

Change those with your AWS credentials that have access to Lambda. You can also change the region where you functions run. In addition you will
need to come up with a way to map incoming URLs to Lambda functions. In my sample VCL I am using 
[Fastly's Edge Dictionaries](https://docs.fastly.com/guides/edge-dictionaries/about-edge-dictionaries) e.g.

<pre>
table url_mapping {
    "/": "/2015-03-31/functions/homePage/invocations",
    "/test": "/2015-03-31/functions/test/invocations",
}

# If no match req.url will be set to /LAMBDA_Not_Found
set req.url = table.lookup(url_mapping, req.url.path, "/LAMBDA_Not_Found" );

# If page has not been found we just throw out a 404
    if ( req.url == "/LAMBDA_Not_Found" ) {
        error 404 "Page not found";
    }
</pre>


### Pros and Cons

Pros:

* You get the power of VCL to route requests to different backends including Lambda
* You may be able to cache some of the requests coming out of Lambda
* Lower costs since API Gateway can be pricey

Cons:

* Only POST requests with payload of up to 2 kbytes and GET requests with no query argument are supported
  * In order to compute the signature we need to calculate a hash of the payload. Unfortunately Varnish exposes
    only 2 kbytes of the payload inside the VCL. This is a tunable if you run your own varnish. You can adjust by
    running
    <pre>
    varnishadm param.set form_post_body 16384 
    </pre>
  * Any request other than POST needs to be rewritten as a POST hence GET can query no argument
* You can output straight HTML however returned payload you will end up with leading and trailing ' character. You will
  also need to fix up the returned Content Type since it returns as application/json. You can set Content Type in VCL by 
  doing following in vcl_deliver e.g.
  <pre>
  set resp.http.Content-Type = "text/html";
  </pre>
* Currently it's impossible to craft POST request froms scratch

### Future work

Look into using something like [libvmod-curl VMOD](https://github.com/varnish/libvmod-curl) to create POST requests on the
fly.