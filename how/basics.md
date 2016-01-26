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

Romana Cloud Native Networks are based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in the IP address. This enables multi-tenant cloud networks to be built without a virtual network overlay.

Romana extends the physical network hierarchy of a [layer 3 routed access design](/how/background/#routed-access-datacenter) from spine and leaf switches on to hypervisor hosts, VMs and containers. Romana then assigns each tenant and segment a physical network CIDR on every host. This lets the Linux kernel forward traffic directly to endpoints and enforce network isolation without the overhead of encapsulation. 

Another advantage of this approach is that route aggregation makes route distribution unnecessary and collapses the number of Linux *iptables* rules required for segment isolation.

Romana includes an intelligent [IP Address Management](/how/romana_details/#ip-address-management) (IPAM) system that assigns IP addresses to VM and container endpoints that maintain the physical address hierarchy. A [Route Manager](/how/romana_details/#route-manager-and-host-agent) configures new routes on the hypervisor and adds firewall rules for tenant isolation and other traffic management policies. 

Service insertion and policy based control (*available in an upcoming  release*) is implemented by reconfiguring the default gateway on endpoint interfaces to steer traffic along specific paths to the IP address of the service endpoint.

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

