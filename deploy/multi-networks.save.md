---
layout: page
title: Multi-network clusters
menu_text: Multi-network clusters
nav_text:
icon:
firstnav: 2
secondnav: 3
permalink: /deploy_romana/multi-newtork-clusters/
---

Romana is network agnostic and supports a wide range deployment options including:

* Flat and split layer 2 networks
* Layer 2 virtual networks
* Layer 3 CLOS/Spine-leaf networks
* Multiple VLAN and multi-segment layer 3 networks
* Routing on the host (RoH) network configurations
* Amazon VPCs

When deploying Kubernetes in the datacenter often there are times when pods requires a multi-segment network.

Pod network segmentation may be required to accommodate existing network topologies, more easily integrate with security infrastructure, or simply because a single contiguous CIDR for all pods is unavailable.

Topology-aware IPAM supports multiple VLANs and layer 3 network segments to accommodate each of these scenarios.

### Multi-VLAN Deployment

When a cluster is shared by applications that requires VLAN isolation, pods and services need IP addresses on the proper network and network policy needs to enforce isolation among pods. Traffic between different VLANs, even when both pods are on a single shared host, must be forwarded to a gateway device where additional cross-VLAN securities can be applied.

The diagram below shows a cluster where pods are launched on two separate VLANs by assigning pods addresses with the VLAN CIDR. Routes are installed for traffic within a VLAN to reach the proper destination host. For traffic across VLANs, Romana network policy is applied so that traffic is forwarded instead to the gateway device.

![Multi-VLAN Deployment]({{ site.baseurl }}/images/multi-vlan.png)

### Multi-segment Layer 3 Deployment

Often network operators want their pod networks to have IPs on the existing datacanter network. These networks are also already partitioned into smaller address blocks.

For large clusters, they may need to combine addresses from two or more separate address ranges to get enough IPs for their pod network. Or it may also be the case that contiguous addresses are not practical or feasible across network availably zones.

Topology aware IPAM can assign addresses from multiple networks anywhere in a cluster, and network advertisement of smaller address blocks avoids overprovisioning of addresses within a zone or rack.

The diagram below shows two separate networks in two availability zones combined for use as a single pod network.

![Multi-segment Layer 3 Deployment]({{ site.baseurl }}/images/multi-segment.png)

