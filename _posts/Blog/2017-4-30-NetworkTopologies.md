---
layout: post
title: Topology Aware IPAM Enables Wide Range of New Network Deployments 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.1, which includes an important new feature for topology aware IP Address Management (IPAM) as well as new options for deployment across EC2 regions and VPCs and more complex layer 3 networks.

Topology aware IPAM is important because it lets users capture the network topology of their deployment so that orchestration systems like OpenStack and Kubernetes can schedule pods and VMs anywhere, without having to also update or distribute new network routes. 

This is especially true for Kubernetes deployments that span EC2 regions and/or VCP subnets as well as OpenStack [Routed Provider Networks](https://docs.openstack.org/ocata/networking-guide/config-routed-networks.html).

New, more complex layer 3 network configurations are enabled by optionally installing route distribution software on one or more nodes. Based on the requirements of the specific cluster deployment, users can install the software that best suites their needs. 

To simply inject routes to pod networks to upstream network devices, [exabg](https://github.com/Exa-Networks/exabgp/wiki) or [Bird]( http://bird.network.cz/) can be configured on every host. To build fault tolerant networks across EC2 regions and VPCs, [Quagga](http://www.nongnu.org/quagga/) can be installed on one or more 'transit' nodes to forward traffic between regions, zones or VCP subnets.

Since there are lots of details to dig into for how these new features can be used, we're providing a separate blog post for several of the common deployment scenarios, including:

* [Flat Layer 2 Networks](/blog/layer-2-networks/)
* [Layer 3 Networks](/blog/layer-3-networks/)
* [Multi-region AWS deployments](/blog/multi-region-networks/)
* [OpenStack Router Provider Networks](/blog/routed-provider-networks/)

 
