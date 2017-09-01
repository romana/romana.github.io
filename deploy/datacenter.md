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

Romana is network agnostic and supports a wide range of deployment options including:

* Flat and split layer 2 networks
* Layer 2 virtual networks
* Layer 3 CLOS/Spine-leaf networks
* Multiple VLAN and multi-segment layer 3 networks
* Routing on the host (RoH) network configurations
* Amazon VPCs

Within the datacenter, Romana is deployed easily on flat or segmented layer 2, or routed layer 3 networks.

### Flat Layer 2 Networks

The most popular network configuration for Kubernetes is on a simple flat layer 2 network segment. Clusters can be built using any layer 2 network technology including simple LANs, tagged VLANs as well as IP fabrics running VXLAN as an overlay virtual network.

In each of these cases, hosts are all on the same segment, which means Romana can configure the pod network by simply installing routes on each host.

No route advertisement is necessary because all pods run on adjacent hosts. Hosts use ARP to learn IPs of all other hosts on the segment and the installed routes indicate which host it should forward traffic to in order to reach pods. Romana utilizes network topology knowledge to configure the host directly, eliminating the need to run route distribution software on nodes.

![Flat Layer 2 Networks]({{ site.baseurl }}/images/FlatL2.png)

*Overlay Virtual Networks*

Many datacenters run virtual networks for greater operational flexibility. These virtual networks typically run as VXLANs across a set of devices configured to terminate the VXLAN tunnels.

Kubernetes clusters run the same on VXLANs as they do on regular LANs or VLANs. The advantage of running Romana is that it does not add a second overlay network for pod communications.


### Routed Layer 3 Networks

New datacenter network designs often use an IP fabric to route traffic across a set of spine and leaf devices.

Hosts attached to different leaf devices are on different subnets but can still communicate because a route is installed to forward traffic. Similarly, the Kubernetes pod network needs routes on these devices to forward traffic between subnets.

Romana supports routed network deployment by advertising the pod network from each host to the upstream routing device. Romana can use either BGP or OSPF to announce these routes. Since the upstream device (typically the top-of-rack switch) will get routes from each host, there is no need for full mesh peering among nodes. For routing on the host configurations, Romana will update the host router directly and let it propagate the routes across the network.

![Routed Layer 3 Networks]({{ site.baseurl }}/images/RoutedL3.png)


### Split Layer 2 Networks

Often, HA clusters are built by assigning hosts to different subnets, creating separate availability zones. This is done by splitting the layer 2 network with a router, as shown below.

![Split Layer 2 Networks]({{ site.baseurl }}/images/SplitL2.png)

For these configurations, in each subnet the Romana agent will install routes on hosts as it normally would for a layer 2 deployment. In addition, Romana will also advertise the pod network upstream to the router so that traffic can reach the other subnet.
