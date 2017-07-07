---
layout: page
title: Datacenter Deployment Options
menu_text: Datacenter
nav_text: Deploy
icon: launch
firstnav: 2
secondnav: 1
permalink: /deploy_romana/datacenter/
---

Romana is network agnostic and supports a wide range of private and public cloud network deployment options including:

* Flat and segmented layer 2 networks
* Layer 2 virtual networks
* Layer 3 CLOS/Spine-leaf Networks
* Routing on the host (RoH) network configurations
* Amazon VPCs

### Flat Layer 2 Networks

The most popular network configuration for Kubernetes is on a simple flat layer 2 network segment. These clusters can be implemented with any network technology including simple LAN, tagged VLANs as well as IP fabrics running VXLAN as overlay virtual network.

In each of these cases, hosts are all on the same segment which means that pod networks can be configured by simply installing routes on the host. No route advertisement is necessary because hosts make ARP requests to lean about all endpoints on the segment. The local Romana agent gathers the route information from the Kubernetes Master node and configures the host directly, eliminating the need to run route distribution software on nodes.

*Overlay Virtual Networks*

Many datacenters run virtual networks to for operational flexibility. These virtual networks typically run as VXLANs across a set of devices configured to terminate the VXLAN tunnels.

Kubernetes clusters run on VXLANs exactly the same as regular VLAN. The advantage of running Romana is that it does not add a second overlay network for pod communications.


### Routed Layer 3 Networks

New datacenter network designs often use an IP fabric to route traffic across a set of spine and leaf devices.

Hosts attached to different leaf devices are on different subnets but can still communicate because a route has been configured to forward traffic. Likewise, the Kubernetes pod network needs routes on these devices to forward traffic between subnets.

Romana supports routed network deployment options by advertising the pod network from each host to the upstream routing device. Romana can use either BGP or OSPF to announce these routes. Since the upstream device (typically the top-of-rack switch) will get routes from each of hosts, there is no need for full mesh peering among nodes. For routing on the host configurations, Romana will update the host router directly and let it propagate the routes across the network.

### Segmented Layer 2 Networks

Often, HA clusters are built by splitting hosts across different subnets. This has the effect of segmeting a layer 2 network with a router, as shown in figure x.

For these configurations, the Romana agent will install routes on hosts as it normally would for a flat layer 2 deployment. However, Romana will also advertise the pod network upstream to the router so that traffic can reach the other subnet.
