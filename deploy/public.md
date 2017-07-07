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

Romana is network agnostic and supports a number of network deployment options in Amazon VPCs.

### Single Availability Zone Deployment

### Multiple Availability Zone Deployment

Network advertisement in EC2

For network advertisement in EC2, Romana v2.0 will update the VCP route table directly via the API in a manner similar to kubnet's EC2 cloud provider function. This lets applications use native VPC networking, avoid an overlay and deliver the highest network performance possible.

In addition, unlike kubnet, since Romana aggregates routes, when you want to build a large cluster you do not need to worry about running out of VPC routes. Romana v2.0 will configure Kubernetes nodes to forward traffic to other nodes, effectively turning them into routers.

Romana performs healthcheck on VPC routes and configures a failover route on instance failure. This now allows users to build HA clusters across availability zones that use native VPC networking.

Together, these new features let users build HA clusters that support network policy, all with native VPC networking.

