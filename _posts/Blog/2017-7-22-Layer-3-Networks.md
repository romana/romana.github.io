---
layout: post
title: Layer 3 Network Deployments 
categories:
- blog
permalink: /blog/layer-3-networks/
---

*This post is the third in a series that [introduces](/blog/topology-aware-IPAM/) Romana's new topology aware IPAM (TA-IPAM) and layer 3 deployment options* 

Running Romana on layer 3 networks introduces the added complexity of configuring the network to know how to reach pods and VMs across subnets. As we learned in the previous [post](/blog/layer-2-networks), layer 2 networks learn about other nodes via broadcast messages. With layer 3 networks, there may be other devices along the path to the nodes and these devices need routes to be added explicitly. 

Every datacenter network supports automatic route distribution. Routing software distributes routes across devices using one or more popular route distribution protocol such as BGP or OSPF. The choice of routing protocol depends on many factors, including the size of the network and other user specific requirements.

To establish VM and container reachability, devices need to be updated with routes to these endpoints. Since Romana assigns complete CIDRs to nodes, only one route is necessary for all endpoints within the range.  Romana v2.0 advertises this route to the upstream network device. From there, the device propagates the routes throughout the network as necessary.

Romana support both OSPF and BGP to accommodate all datacenter deployments. Node peering to only the upstream device avoids node-to-node (i.e. full mesh) peering and route aggregation to full CIDRs eliminates the overhead of route updates with each added endpoint.

With support for both layer 2 and layer 3 networks using industry standard protocols, Romana can now deployed using any network design or vendor technology. 

[Insert Diagram Here}

### AWS Deployments

For deployments in EC2 VPCs that span multiple Availability Zones (AZs), route configuration is also required. Romana v2.0 includes support to update native VPC routing. This enables clusters to span availability zones without an overlay. 

AWS deployments that span AZs frequently need more VPC routes than are allowed (50 by default). An overlay network is a common way to get around this limitation, but that adds complexity and reduces performance and visibility. 
With Romana v2.0, nodes can be configured to forward traffic to other nodes within the same zone, enabling large clusters to use native VPC networking while still avoiding the VPC route limit. 

