---
layout: post
title: OLD Topology Aware IPAM Post in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.5, which includes important new features including topology aware IP Address Management (TA-IPAM) for multi-network deployments. Romana v1.5 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) providing new installation options on bare metal servers and across EC2 VPC Availability Zones.

Topology aware IPAM is important because it lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes can schedule pods and VMs across multiple networks, without the need to update routers. 

Multi-network clusters are part of every high-availability configuration that split clusters across network availability zones as well as datacenter deployments on routed layer 3 networks. 

Topology aware IPAM and multi-network support also lets users secure their pods and VMs using existing data center security infrastructure by steering traffic into their existing security devices where existing security policy can be enforced. 

Topology aware IPAM is also important when IP addresses are scarce because it avoids the overprovisioning of addresses that typically occurs to allow flexible resource placement (as in OpenStack [Routed Provider Networks](https://docs.openstack.org/ocata/networking-guide/config-routed-networks.html), for example). 

New datacenters today often use a layer 3 [leaf/spine](http://searchdatacenter.techtarget.com/feature/Data-center-network-design-moves-from-tree-to-leaf) network design. For on premises Kubernetes deployments, this often means each node is on its own separate network. With this kind of network topology, routes to pod networks (and to VMs in OpenStack) need to be injected into upstream network devices. Romana v1.5 now supports these network configurations automatically by optionally installing route distribution software on one or more nodes. 

Based on the requirements of the specific cluster deployment, users can install route distribution software that best suits their needs. For simple flat networks (i.e layer 2 networks), Romana directly configures routes on nodes and no other software is needed.

To inject routes to pod networks on upstream network devices, Romana v1.5 can now configure either [exabg](https://github.com/Exa-Networks/exabgp/wiki) or [Bird](http://bird.network.cz/) on hosts. If you'd rather use a full featured router, or want to build secure, fault tolerant networks across EC2 regions, zones and providers all without an overlay, [Quagga](http://www.nongnu.org/quagga/) can be used to forward traffic between networks.

Together, these new TA-IPAM and route distribution features enable a variety of new standard, HA and hybrid configuration options for datacenter deployment and across the public cloud.

Since there are lots of details to dig into for how these new features can be used, we're providing a separate blog post for the most the common deployment scenarios, including:

* [Flat Layer 2 Networks](/blog/layer-2-networks/)
* [Layer 3 Networks](/blog/layer-3-networks/)
* [Multi-region AWS deployments](/blog/multi-region-networks/)
* [Hybrid cloud deployment with Federated Kubernetes Clusters](/blog/federated-cloud)
* [OpenStack Router Provider Networks](/blog/routed-provider-networks/)

Romana v1.5 is available today, [here](https://github.com/romana/romana) on GitHub. 
