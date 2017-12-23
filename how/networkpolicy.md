---
layout: page
title: Network Security with Romana
menu_text: Network Policy
nav_text: NA
firstnav: 1
secondnav: 3
permalink: /how/network-policy/
---

Romana supports Kubernetes Network Policy API for pod isolation and microsegmentation of namespaces. Topology aware IPAM allows more restrictive route filtering for greater network security.

### Microsegmentation and Isolation

Romana isolates endpoints with traffic filters applied to network endpoints. Network interfaces tap directly into the kernel providing isolation from other endpoints on the host that might otherwise share a linux bridge where Ethernet frames flow freely. This lets packet filtering at layer 3 using iptable rules enforce isolation and network security policy.

Romana supports Kubernetes Network Policy APIs for namespace isolation as well as its own expanded network policy enforcement capability for both ingress and egress traffic filtering and host protection policies.

Network policy based on pod label and metadata allows flexible security policies to be enforced on arbitrary network endpoint collections, independent of network topology or addressing.

### Route Filtering

Romana allow more restrictive route filters in network devices because topology aware IPAM ensures addresses remain with specific network zone. See [Route Filtering](/deploy_romana/multi_network_clusters/#route-filtering) for more details.

