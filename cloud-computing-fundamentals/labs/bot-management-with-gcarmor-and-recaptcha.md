# Bot Management with Google Cloud Armor and reCAPTCHA


## Overview

Load balancing is the process of distributing network or application traffic across multiple servers to ensure no single server becomes overwhelmed, thus improving responsiveness and availability.

Google Cloud HTTP(S) load balancing is deployed at the edge of Google's network in Google points of presence (POP) around the world. User traffic directed to an HTTP(S) load balancer enters the POP closest to the user and is then load balanced over Google's global network to the closest backend that has sufficient capacity available.

Cloud Armor is Google's distributed denial of service and web application firewall (WAF) detection system. Cloud Armor is tightly coupled with the Google Cloud HTTP Load Balancer and safeguards applications of Google Cloud customers from attacks from the internet.

reCAPTCHA Enterprise is a service that builds on the reCAPTCHA API and protects your site from spam and abuse using advanced risk analysis techniques to tell humans and bots apart. Cloud Armor Bot Management provides an end-to-end solution integrating reCAPTCHA Enterprise bot detection and scoring with enforcement by Cloud Armor at the edge of the network to protect downstream applications.

setting up project ID:
```
export PROJECT_ID=$(gcloud config get-value project)
echo $PROJECT_ID
gcloud config set project $PROJECT_ID
```

## 1. Configure firewall rules to allow HTTP and SSH traffic to backends

## 2. Configure instance templates and create managed instance groups
> A managed instance group uses an instance template to create a group of identical instances. Use these to create the backend of the HTTP Load Balancer.
