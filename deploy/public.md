---
layout: page
title: Amazon EC2 Deployment Options
menu_text: Public Cloud
nav_text:
icon:
firstnav: 2
secondnav: 2
permalink: /deploy_romana/public_cloud/
---

Romana is network agnostic and supports a wide range deployment options including:

* Flat and segmented layer 2 networks
* Layer 2 virtual networks
* Layer 3 CLOS/Spine-leaf Networks
* Routing on the host (RoH) network configurations
* Amazon VPCs

Amazon EC2 is one of the most popular platforms for running but when it comes to networking, EC2 has some limits that make deploying production Kubernetes clusters unnecessarily complex and unable to operate with peak performance. 

Using Roman avoids these limitations and delivers native VPC network performance for every cluster. eliminates the requirement for an overlay, even when clusters are split across Availability Zones, large, multi-zone HA clusters.

### Single Availability Zone Deployment

EC2 instances running in a single VPC Availability Zone can rely on routes installed by Romana to reach all pods. No VPC routes are necessary.

### Multiple Availability Zone Deployment

Network advertisement in EC2

For network advertisement in EC2, Romana v2.0 will update the VCP route table directly via the API in a manner similar to kubnet's EC2 cloud provider function. This lets applications use native VPC networking, avoid an overlay and deliver the highest network performance possible.

In addition, unlike kubnet, since Romana aggregates routes, when you want to build a large cluster you do not need to worry about running out of VPC routes. Romana v2.0 will configure Kubernetes nodes to forward traffic to other nodes, effectively turning them into routers.

![AWS VPC Availability Zones]({{ site.baseurl }}/images/VPCZones.png)


Romana performs healthcheck on VPC routes and configures a failover route on instance failure. This now allows users to build HA clusters across availability zones that use native VPC networking.

Together, these new features let users build HA clusters that support network policy, all with native VPC networking.

