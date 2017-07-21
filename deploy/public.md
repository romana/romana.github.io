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

Amazon EC2 is one of the most popular platforms for running Kubernetes but when it comes to networking, EC2 has some limits that make deploying production clusters unnecessarily complex and unable to operate with peak performance.

Romana lets users avoid these limitations and deploy production clusters across availability zones without an overlay and deliver native VPC network performance at any scale.


### Single Availability Zone Deployment

Kubernetes pods on instances running in a single VPC Availability Zone use routes installed by Romana to reach all pods. No VPC routes are necessary. Routes are installed on each host the same as when deployed in the datacenter on a [flat layer 2 network](/deploy_romana/datacenter/#flat-layer-2-networks).


### Multiple Availability Zone Deployment

Highly available clusters are typically split across subnets in different availability zones and require VPC routes for traffic between subnets. Romana avoids the limit on the number of VPC routes (default of 50) which caps the size of clusters that can use native VPC networking.

#### Network Advertisement in EC2

Romana updates the VCP route table directly via the API. This lets applications use native VPC networking, avoid an overlay and deliver the highest network performance possible.

Romana adds a route for each node, up to the VPC limit. 

![AWS VPC Availability Zones]({{ site.baseurl }}/images/VPCZones.png)

For large clusters, Romana will configure nodes to forward traffic to other nodes, effectively turning them into routers. Traffic is spread across available nodes to avoid traffic bottlenecks.

![AWS VPC Availability Zones]({{ site.baseurl }}/images/VPCZones.51.png)

Romana performs healthcheck on VPC routes and configures a failover route on instance failure. This now allows users to build HA clusters of any size, across availability zones all while using native VPC networking.

