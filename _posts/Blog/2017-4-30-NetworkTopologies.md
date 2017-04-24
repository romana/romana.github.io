---
layout: post
title: Topology Aware IPAM Enables Wide Range of New Network Deployments 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.1, which includes important new features for topology aware IP Address Management (IPAM) and operation in more complex layer 3 networks. Romana v1.1 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) providing new installation options on bare metal servers as well as deployment across EC2 regions and VPCs.

Topology aware IPAM is important because it lets users capture the network topology of their deployment so that orchestration systems like OpenStack and Kubernetes can schedule pods and VMs anywhere without having to also update or distribute new network routes. 

This is especially useful when IP addresses are scarce because it avoids the overprovisioning of network addresses that typically occurs to allow flexible resource placement (as in OpenStack [Routed Provider Networks](https://docs.openstack.org/ocata/networking-guide/config-routed-networks.html), for example). Topology aware IPAM is also important for Kubernetes deployments that span networks (including EC2 regions and VCP subnets) since it simplifies deployment by enabling route aggregation for traffic between networks. 

New datacenters today often use a [layer 3 leaf/spine](http://searchdatacenter.techtarget.com/feature/Data-center-network-design-moves-from-tree-to-leaf) network design. For on premises Kubernetes deployments, this often means each node is on a separate network. With this kind of network topology, routes to pod networks (and to VMs with OpenStack) need to be injected into upstream network devices. Romana v1.1 now supports these more complex layer 3 network configurations by optionally installing route distribution software on one or more nodes. 

Based on the requirements of the specific cluster deployment, users can install the software that best suits their needs. For simple flat networks (i.e layer 2 networks), Romana directly configures routes on nodes and no other network software is required.

To inject routes to pod networks on upstream network devices, Romana v1.1 can now configure either [exabg](https://github.com/Exa-Networks/exabgp/wiki) or [Bird]( http://bird.network.cz/) on hosts. To build fault tolerant networks across EC2 regions and VPCs, [Quagga](http://www.nongnu.org/quagga/) can be installed on one or more 'transit' nodes to forward traffic between regions, zones or VCP subnets.

Together, these new IPAM and route distribution features available in Romana v1.1 open up all kinds of new standard and HA configurations options.

Since there are lots of details to dig into for how these new features can be used, we're providing a separate blog post for the most the common deployment scenarios, including:

* [Flat Layer 2 Networks](/blog/layer-2-networks/)
* [Layer 3 Networks](/blog/layer-3-networks/)
* [Multi-region AWS deployments](/blog/multi-region-networks/)
* [OpenStack Router Provider Networks](/blog/routed-provider-networks/)

Romana v1.1 is available today, [here](https://github.com/romana/romana) on GitHub. 
