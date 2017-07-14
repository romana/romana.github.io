---
layout: page
title: Romana Details
menu_text: More Details
nav_text: NA
firstnav: 1
secondnav: 2
permalink: /how/romana_details/
---

Romana uses topology-aware IP address Management (IPAM) in conjunction with route configuration and advertisement to build simple, scalable, high performance Cloud Native networks.

### Topology Aware IPAM 

Orchestration systems like Kubernetes are configured with a network CIDR with addresses for pod and service endpoints. This network might have 16k or more addresses available for the pod network. Addresses in this network are typically split and assigned to hosts without regard for the network topology (i.e. subnets). This works fine for flat network topologies where each host knows about all the other hosts on the network segment. Routes on the host to all the other hosts is all that is necessary for all pods to be reachable. 

Problems, however, emerge when hosts are on different subnets. This is typical when clusters are split across network zones for high availability. In these HA deployments, hosts are not aware of hosts in the other subnets and need routes in the network to reach the other zones. 

Topology aware IPAM is a way to minimize the route information that is required by the network. For example, if the full pod network CIDR is split into subnets, the network requires only one route for each subnet. *However*, operation with only one route per subnet is possible *only* if Kubernetes is aware of the network topology and can assign IP addresses based on which subnet the host is located. Romana's topology-aware IPAM enables this kind of pod scheduling. 

Romana allows pod networks to be specified by one or more network prefix. Each prefix is then split into address groups that are assigned to each network zone. Romana's *topology-aware IPAM* ensures pods only get addresses from the group that is associated with a zone. Since within a zone, all pods have addresses from within the same network prefix, they all are reachable via the same route in the network, eliminating additional route updates. 

Efficient use of addresses and flexibility of host deployment options is possible by allowing address groups to be added to a zone as necessary. 

### Network Advertisement 

Configuring network devices with routes is best done by network advertisement using standard network routing protocols. Romana supports industry standard protocols including BGP and OSPF. Because Romana configures routes to all hosts within subnet directly, network advertisement is only necessary for routes between subnets. This is achieved by peering with the router that forwards traffic between zones, typically a top-of-rack device.

Configuring local subnet routes on hosts directly avoids full-mesh peering that would be required to advertise routes to all hosts in a zone. 

*VCP Route Advertisement* 

For network advertisement in EC2, Romana will update the VCP route table directly via the API in a manner similar to kubnet's EC2 cloud provider function. This lets applications use native VPC networking, avoid an overlay and deliver the highest performance VPCs can provide.

In addition, unlike kubnet, since Romana aggregates routes, when you want to build a large cluster you do not need to worry about running out of VPC routes. Romana will configure Kubernetes nodes to forward traffic to other nodes, effectively turning them into routers.

Romana performs healthcheck on VPC routes and configures a failover route on instance failure. This now allows users to build HA clusters across availability zones that use native VPC networking.

Together, these new features let users build HA clusters that support network policy, all with native VPC networking.

### Micro-segmentation and Isolation

Romana isolates endpoints with traffic filters applied to network CIDRs. Network interfaces tap directly into the kernel providing isolation from other endpoints on the host that might otherwise share a linux bridge where Ethernet frames flow freely. This lets packet filtering at layer 3 using iptable rules enforce isolation and network security policy. 

Applying network policy to complete CIDRs v. individual endpoints reduces the number of filter rules that need to be applied, reducing complexity and simplifying operations. 
