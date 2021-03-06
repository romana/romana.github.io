---
layout: page
title: Romana Details
menu_text: More Details
nav_text: NA
firstnav: 1
secondnav: 2
permalink: /how/romana_details/
---

Romana uses topology-aware IP address Management (IPAM) in conjunction with route configuration and advertisement to build simple, scalable, high performance cloud native networks.

### Topology Aware IPAM 

Orchestration systems like Kubernetes are configured with a network CIDR with addresses for pod and service endpoints. A typical network might have 16k or more addresses available for the pods. Addresses in this network are split and assigned to hosts without regard for network topology (i.e. subnets). This works fine for simple, flat network topologies where each host knows about all the other hosts on the network segment. Routes on the host to all the other hosts is all that is necessary for all pods to be reachable. 

Problems, however, emerge when hosts are on different subnets. This is typical when clusters are split across network zones for high availability. In these HA deployments, hosts are not aware of hosts in the other subnets and need routes in the network to reach the other zones.

Topology aware IPAM is a way to minimize the route information that is required by the network. With Romana, the full pod network CIDR is split into subnets that reflect network topology so that only a single route to each subnet is required. This operation is only possible when Kubernetes is made aware of the topology so that it can assign IP addresses based on which subnet a pod is located.

Romana enables this kind of pod scheduling. *Topology-aware IPAM* ensures pods only get addresses from within the correct subnet range. This way they all are reachable via a single route to the subnet. And since this route is valid for all addresses in the range, route updates are not necessary when new endpoints are added.

Efficient use of addresses and flexibility of host deployment options is possible by allowing new networks and subnets to be added as necessary.

### Network Advertisement 

Configuring network devices with routes is best done by network advertisement using standard network routing protocols. Romana supports industry standard protocols including BGP and OSPF. Because Romana uses its local agent to directly configure routes to other hosts within the same subnet, network advertisement is only necessary for routes between subnets. This is achieved by peering with the router that forwards traffic between zones, typically a top-of-rack device. 

Configuring local subnet routes on hosts directly avoids full-mesh peering that would be required to advertise routes to all hosts in a zone. 

Topology aware IPAM means that network devices need only a default route to upstream routers, enabling a more secure network.

*VPC Route Advertisement* 

For network advertisement in EC2, Romana updates the VPC route table directly via the API in a manner similar to kubnet's EC2 cloud provider function. This lets applications use native VPC networking, avoid an overlay and deliver the highest network performance VPCs can provide.

In addition, unlike kubnet, Romana requires only one route between subnets, so when you want to build a large cluster you do not need to worry about running out of VPC routes. Romana will configure Kubernetes nodes to forward traffic to other nodes, effectively turning them into routers.

Romana performs healthchecks on VPC routes and configures a failover route on instance failure. This now allows users to build HA clusters across availability zones that use native VPC networking.

Together, these new features let users build HA clusters that support network policy, all with native VPC networking.
