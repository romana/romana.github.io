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

<<<<<<< HEAD
Topology aware IPAM is a way to minimize the route information that is required by the network. For example, if the full pod network CIDR is split into subnets, the network requires only one route for each subnet. *However*, operation with only one route per subnet is possible *only* if Kubernetes is aware of the network topology and can assign IP addresses based on which subnet the host is located. Romana's topology-aware IPAM enables this kind of pod scheduling. 
=======
---

### Introduction

Cloud Native Networks are built and managed by Romana's configuration and control services. Central to this approach is intelligent [IP Address Management](/how/romana_details/#ip-address-management) and [route configuration](/how/romana_details/#host-agent) that builds complete layer 3 networks on hosts. Romana then creates a gateway and routes to these networks on hosts so that the Linux kernel can forward traffic directly to endpoints and enforce network policy without the overhead of encapsulation.

---

### Romana Tenant Isolation

A common way to maintain tenant isolation is to use VXLANs to overlay individual layer 2 segments on top of a layer 3 underlay network. However, since Cloud Native applications do not require layer 2 networks, Romana can avoid an overlay network for tenant isolation as long as isolation can be enforced at layer 3.

Fortunately, isolation can be achieved at layer 3 with a host based firewall by configuring Linux *iptables* rules on the hypervisor to allow communication only among valid endpoints. The local Romana Agent is available to configure these firewall rules as needed. 

Maintaining these firewall rules and access control list (ACLs) for all endpoints quickly grows into a large and complex data management problem. The problem becomes even more difficult when sophisticated traffic management policies are required.

To reduce rule management complexity and simplify the network further, Romana extends the way layer 3 network designs capture network topology in the IP address by also including a tenant and segment identifier. 

In the same way that a layer 3 network design assigns a network CIDR to every physical switch port, Romana assigns, a CIDR for each tenant.  Similarly, within each tenant network, each segment gets its own network as well.

The diagram below is an example of how Romana can partition an IPv4 address to capture host, tenant and segment identity. 

![Route Aggregation]({{ site.baseurl }}/images/cidr.png)

> Note: For simplicity, the example shown uses 8 bits for each range, which makes the resulting IP addresses readable in four octet dot address notation. The actual number of bits used for Host, Tenant and Segment IDs is configurable. 

In this example all traffic for Host 1 (Host ID = 1), would go to the 10.1/16 network. Of this, traffic to Tenant 1 (ID=1) would be directed to the 10.1.1/24 network. Traffic for Tenant 2 (ID=2) would go to 10.1.2/24. Traffic to Tenant 1 Segment 1 (ID=1) would go to 10.1.1.16/28 (1 in bits 25-28 of address, or 00010000=16). For Host 2, all CIDRs would be the same, except that 1 would be replaced by 2, the ID of Host 2.

Endpoints that are in a specific segment would have IP addresses from the CIDR range of the segment. VM1 (ID=11) on Host 1 for Tenant 1 Segment 1 would get 10.1.1.27 (16+11=27).

In summary, for each virtualization host, there is a CIDR that identifies traffic to the entire host; a longer CIDR that identifies traffic to each tenant on the host; and for each tenant, there is an even longer CIDR that identifies traffic to each segment. 
>>>>>>> refs/remotes/origin/master

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

