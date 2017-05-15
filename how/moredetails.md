---
layout: page
title: Romana Details
menu_text: More Details
nav_text: NA
firstnav: 1
secondnav: 2
permalink: /how/romana_details/
---

### Introduction

Cloud Native Networks are built and managed by Romana's configuration and control service that manages routes, policy and IP addresses across network endpoints. This Romana service communicates with agents running on hosts to configure endpoints. Together, they build on-host networks for VMs and container endpoints so that the Linux kernel can forward traffic and enforce network policy. Topology aware IP address management lets the network use existing aggregated routes, eliminating the need for a virtual network overlay and route updates in the network when adding new endpoints.

![Romana Details]({{ site.baseurl }}/images/RomanaDetails.png)

---

### Network Isolation

A common way to maintain network isolation is to use a layer 2 virtual network overlay (VXLAN). However, since Cloud Native applications do not require layer 2 networks, Romana can avoid an overlay by providing the necessary endpoint isolation directly on the host using standard Linux IP packet filtering.

The local Romana agent builds a host-based firewall by configuring Linux *iptables* rules on the host to allow communication only among valid endpoints. Firewall rules and access control list (ACLs) are compiled from a network policy that describes valid endpoint communications.

Rules for all endpoints can quickly grow into a large, complex data management problem. The problem becomes even more difficult when sophisticated traffic management policies are required.

Romana reduces rule management complexity by assigning a complete CIDR to each isolated segment so that policy rules can be applied more easily to all endpoints within the range. 

### Topology Aware IP Address Management

Topology Aware IP Address Management (TA-IPAM) is a way for Romana to assign CIDR blocks so that existing network configurations remain intact, eliminating the need for an overlay network or route distribution. With Romana, even HA configurations that span network availability zones can run [without an overlay](/how/network_configurations). 

Complete CIDRs on hosts, allocated using TA-IPAM, enables route aggregation and simplifies policy enforcement. However, for efficient use of IP addresses, CIDR blocks should not be too large since even one isolated endpoint reserves all the addresses in the block. Romana provides flexibility with configurable length CIDRs (default /29 for 8 addresses per block). For isolated segments larger than a single block would allow, blocks are grouped, as necessary, into a single larger segment. 

Topology aware IPAM lets AWS VPC deployments run across availability zones using only VPC routes, eliminating the need for tunnels or an overlay network. 

The following Romana configuration shows how easy it is represent the topology of a three AZ configuration using VPC routes:

<pre><code>
{
"type" : "vpc",
"num-azs" : 3,
"prefix-groups" : "max",
"network" : "10.192.0.0/16"
}
</code></pre>

### Policy Based Control

Romana supports fine-grained control of network traffic via network policies. Romana network policies can be applied to ingress and egress traffic on VM and container endpoints, as well as on the host itself. This lets Romana enforce a range of security policies that is beyond what is possible with standard OpenStack Security Groups or the Kubernetes [Network Policy API](https://kubernetes.io/docs/concepts/services-networking/networkpolicies/). 

Egress traffic filtering lets Romana prevent access to other datacenter resources or cloud services that might otherwise be accessible on the network.

See the Romana GitHub [Wiki](https://github.com/romana/romana/wiki/Romana-policies) for  examples of egress policies [protecting AWS RDS services](https://github.com/romana/core/tree/master/policy/examples) and other host resources.

### Host Agent

The Romana Agent runs on every host and watches an etcd datastore for configuration updates. Romana uses the native routing functions in the Linux kernel for packet forwarding and does not require any additional kernel software (dataplane kernel modules, etc.). Configuring routes on the host is done with standard Linux *ip route* commands, which are issued locally via the Romana Agent.

For Kubernetes deployments there is also a CNI plugin that configures pod interfaces as necessary.
