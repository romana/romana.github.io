---
layout: page
title: Just the Basics
nav_text: How it Works
menu_text: Basics
icon: build
firstnav: 1
secondnav: 1
permalink: /how/romana_basics/
---

Romana creates isolated Cloud Native networks and applies security and network policy to them using standard layer 3 networking techniques. Just like networks on the public internet, isolation and access is based on IP address ranges that identify what traffic is allowed and where it can flow. Romana uses these familiar layer 3 techniques to build secure, Cloud Native networks without a virtual network overlay.

Romana controls IP addresses on VM and container endpoints through an [IP Address Management](how/romana_details/) (IPAM) system which lets complete layer 3 networks become the unit of isolation. Romana then creates a gateway and routes to these networks on hosts so that the Linux kernel can forward traffic directly to endpoints and enforce network policy without the overhead of encapsulation.

An important advantage of this approach is that route aggregation makes route distribution unnecessary and collapses the number of Linux *iptables* rules required for segment isolation.

Even though Romana uses a layer 3 isolation model, it can run on layer 2 or layer 3 networks, as well as on public cloud networks like Amazon's VPC.

More details on how Romana works are available [here](/how/romana_details/). 

Background information in the form of a brief overview of layer 3 routed access datacenter [design](/how/background/#routed-access-datacenter),  VXLAN tenant [isolation](/how/background/#vxlan-tenant-isolation) and service chaining [techniques](/how/background/#service-insertion) 
are available [here](/how/background/).

Or jump right to the topic you want to learn more about.

---

### Romana Details  

- [Introduction](/how/romana_details/#introduction)
- [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation)
- [IP Address Management](/how/romana_details/#ip-address-management)
- [Route Manager and Host Agent](/how/romana_details/#route-manager-and-host-agent) 
- [Microservices](/how/romana_details/#microservices) 
- [Service Insertion](/how/romana_details/#service-insertion) 
- [Policy Based Control](/how/romana_details/#policy-based-control)  


---

### Technology Background 

- [Technology Background](/how/background/)
- [Routed Access Datacenter](/how/background/#routed-access-datacenter)
- [VXLAN Tenant Isolation](/how/background/#vxlan-tenant-isolation)
- [Service Insertion](/how/background/#service-insertion) 

