---
layout: page
title: Romana Network Configurations
menu_text: Network Configurations
nav_text: NA
firstnav: 1
secondnav: 3
permalink: /how/network_configurations/
---

### Network Configurations

Romana supports layer 2, layer 3 and Amazon VPC network configurations. Romana captures [network topology](/how/romana_details/) so that once the network is configured, VM and container endpoints can come and go without reconfiguration. Romana advertises on-host networks so that the external devices can learn how to reach all endpoints.

### Layer 2 Networks

For small clusters on a datacenter VLAN, or when running in a single AWS VPC subnet, each host can learn the network configuration of all the other hosts on the network so no network advertisement is necessary. This allows the Romana agent to configure the IP address of the other hosts as the next hop router address for traffic across hosts. 

When Romana is first installed, or when a new host is added to a cluster, Romana creates a route to every other host. Since these inter-host routes are valid for every endpoint created on hosts, no additional route updates are necessary.

Since hosts can only learn about the other hosts on the same layer 2 subnet, for configurations that span subnets additional network configuration is necessary

### Layer 3 Networks

For larger deployments or HA configurations that span network availability zones, hosts on other subnets must advertise a route to their endpoints so the network will forward traffic correctly.

Romana configurations include network topology information used for route advertisement and IP address management. Topology information is captured simply by including a list of hosts and the subnets they are part of. 

For example, to indicate a routed network with two subnets, the Romana spec would simply indicate the subnet each host was running on:

<pre><code>
{
"type" : "routed",
"subnets" : 2,
"subnet" : { "SubnetA" , "hosts": "Node1","$Node1_IP", NodeB" "$NodeB_IP"}
"subnet" : { "SubnetB" , "hosts": "Node3","$Node3_IP", Node4" "$Node4_IP"}
"network" : "10.234.192.0/18"
"
}
</code></pre>

#### Route Advertisement

This specification provides the information needed for topology aware [IP address assignment](/how/romana_details/) as well as route advertisement.

Routes may be advertisement by BGP agents, route reflectors and other network infrastructure to automatically update the network.

For Amazon VPC subnets, the Romana agent will add routes automatically to the VPC route table. The agent will add one route for each EC2 instance so that traffic has a direct path to the correct instance. 

For very large VPC configurations that require routes beyond the limit of the VCP route table, Romana topologies can be specified to use an E2 instance as a routing instance to forward traffic to the destination node.

Healthchecks are run against routing instances so that when a routing instance fails, routes will be updated to ensure the network is always available.
