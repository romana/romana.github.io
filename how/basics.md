---
layout: page
title: Just the Basics
nav_text: How it Works
menu_text: Basics
icon: build
firstnav: 2
secondnav: 1
permalink: /how/romana_basics/
---

Romana Cloud Native Networks are based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in the IP address. This enables multi-tenant cloud networks to be built without a virtual network overlay.

Romana includes an intelligent [IP Address Management](/how/romana_details/#ip-address-management) (IPAM) system that assigns IP addresses that capture network topology and application level context. This lets Romana take advantage of modern, layer 3 routed access datacenter designs, and allows routes to be aggregated, and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

A [Route Manager](/how/romana_details/#host-agent-and-route-manager) 
configures new routes on the hypervisor and uses the application level context in the addresses to simplify configuration of firewall rules for tenant isolation and other traffic management policies. 

Also, since routes on network devices are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion and policy based control (*available in the next release*) is implemented by reconfiguring the default gateway on endpoint interfaces to steer traffic along specific paths to the IP address of the service endpoint.

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

