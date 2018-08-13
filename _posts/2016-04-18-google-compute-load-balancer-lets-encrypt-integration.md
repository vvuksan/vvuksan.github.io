---
author: admin
comments: false
date: 2016-04-18 18:00:00+00:00
layout: post
slug: google-compute-load-balancer-lets-encrypt-integration
title: Google Compute Engine Load balancer Let's Encrypt integration
tags:
- terraform
- Google Cloud Engine
- Cloud
---

[Let's Encrypt](https://letsencrypt.org/) (LE) is a new service started by Internet Security Research Group (ISRG)
to offer free SSL certificates. It's intended to be automated so that you can obtain a certificate quickly and 
easily. Currently however LE requires installation of their client software which makes a request to their API 
for a domain you want to secure then generates a random script that it puts at a random Web Path for the 
domain so that LE backend servers can check them. In a nutshell to get a certificate for  domain *myhost.mydomain.xyz* 
LE client will require you add predetermined text at a URL they provide e.g.

> http://myhost.mydomain.xyz/.well-known/jdoiewerhwkejhrwehrheuwhruewh

If that matches you have validated you are the owner of the domain and LE
issues you a certificate. [More detail on how it works can be found here](https://letsencrypt.org/how-it-works/).

Difficulty is that in order to automate this process you either 

* have to allow LE client to control your web server (currently only Apache) - this may disrupt your traffic in case of any issues
* allow it to drop files into a web root which may be problematic if your domain is behind load balancer and you need
  to copy the validation content to all nodes
* use standalone method where LE spins it's own standalone server but requires you to shut down your web server 
* devise a different method

In following section I will describe a method on how to do this with Google Cloud Engine (GCE) Load balancer
since it supports conditional URL path matching. You could also do something very similar with other load balancers
such as Varnish or Haproxy.

Conceptually what we'll do is 

* Modify the GCE Load balancer URL map to send all traffic intended for LE to a special
  backend e.g. any URL with /.well-known/ will be sent to a custom backend
* Spin up a minimal VM with Apache on GCE
* Use the LE client Docker image to manage the signing process or simply install the LE client

To make configuration easy I will be using [https://www.terraform.io](Terraform) since it greatly
simplifies this process. This process also assumes you are already running GCE load balancer against
the domain you are trying to secure.

First we'll need to create an instance template. I am using the Google Container Engine
images as they already come with Docker installed.

<pre>
variable "gce_image_le" {
    description         = "The name of the image for Let's Encrypt."
    default             = "google-containers/container-vm-v20160321"
}

resource "google_compute_instance_template" "lets-encrypt" {
    name                = "lets-encrypt"
    machine_type        = "f1-micro"
    can_ip_forward      = false
    tags                = [ "letsencrypt", "no-ip" ]

    disk {
        source_image    = "${var.gce_image_le}"
        auto_delete     = true
    }

    network_interface {
        network         = "${var.gce_network}"
        # No ephemeral IP. Use bastion to log into the instance
    }

    metadata {
        startup-script  = "${file("scripts/letsencrypt-init")}"
    }

}
</pre>

You will notice I am using a startup script (scripts/letsencrypt-init) inside this instance template which 
looks like this

<pre>
apt-get update
apt-get install -y apache2
rm -f /var/www/index.html
touch /var/www/index.html
docker pull quay.io/letsencrypt/letsencrypt:latest

mkdir /root/ssl-keys
echo "email = myemail@mydomain.com" > /root/ssl-keys/cli.ini
</pre>

Basically I'm just preinstalling Apache and pulling the Let's Encrypt Client Docker Image.

Next step is to create an Instance Group Manager (IGM) and Autoscaler. Instance group manager defines
what instance template is gonna be used and base instance name whereas autoscaler starts up instances in
IGM and makes sure there is one replica running. Last step is to define the backend service and
attach IGM to it.

<pre>
resource "google_compute_instance_group_manager" "lets-encrypt-instance-group-manager" {
    name                = "lets-encrypt-instance-group-manager"
    instance_template   = "${google_compute_instance_template.lets-encrypt-instance-template.self_link}"
    base_instance_name  = "letsencrypt"
    zone                = "${var.gce_zone}"

    named_port {
        name            = "http"
        port            = 80
    }

}

resource "google_compute_autoscaler" "lets-encrypt-as" {
    name                = "lets-encrypt-as"
    zone                = "${var.gce_zone_1_fantomtest}"
    target              = "${google_compute_instance_group_manager.lets-encrypt-instance-group-manager.self_link}"
    autoscaling_policy = {
        max_replicas    = 1
        min_replicas    = 1
        cooldown_period = 60
        cpu_utilization = {
            target = 0.5
        }
    }
}

resource "google_compute_backend_service" "lets-encrypt-backend-service" {
    name                = "lets-encrypt-backend-service"
    port_name           = "http"
    protocol            = "HTTP"
    timeout_sec         = 10
    region              = "us-central1"

    backend {
        group           = "${google_compute_instance_group_manager.lets-encrypt-instance-group-manager.instance_group}"
    }

    health_checks       = ["${google_compute_http_health_check.fantomtest.self_link}"]    
    
}
</pre>

Next thing we'll need to do is change the URL map for the load balancer. Basically we'll
send anything matching /.well-known/* to our LE backend service. My URL map is called fantomtest
that by default uses the fantomtest backend service. This means any requests that don't match 
/.well-known/ will end up on my default backend service (which is what we want)

<pre>
resource "google_compute_url_map" "fantomtest" {
    name                = "fantomtest-url-map"
    description         = "Fantomtest URL map"
    default_service     = "${google_compute_backend_service.fantomtest.self_link}"

    # Add Letsencrypt
    host_rule {
        hosts           = ["*"]
        path_matcher    = "letsencrypt-paths"
    }

    path_matcher {
        default_service = "${google_compute_backend_service.fantomtest.self_link}"
        name            = "letsencrypt-paths"
        path_rule {
            paths       = ["/.well-known/*"]
            service     = "${google_compute_backend_service.lets-encrypt-backend-service.self_link}"
        }
    }

}
</pre>

Terraform apply it and if you have been successful you should see the letsencrypt service become healthy.

Now log into the instance running the LE client and run

<pre>
docker run -it -v "$(pwd)/ssl-keys:/etc/letsencrypt" -v "/var/www:/var/www" quay.io/letsencrypt/letsencrypt:latest \
  certonly --webroot -w /var/www -d www.mydomain.xyz
</pre>

If you get 

<pre>
- Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/www.mydomain.xyz/fullchain.pem. Your
   cert will expire on 2016-07-17. To obtain a new version of the
</pre>

You are done and your certificate will be found in ssl-keys/live/www.mydomain.xyz/fullchain.pem. By default LE issues
certificates with validity of 90 days and they will nag you starting 30 days before expiration to update them. I will
leave it as an excercise to the reader to automate this. Do note that if you are gonna automate pushing certificates
make sure you validate the full chain to make sure things look good.

