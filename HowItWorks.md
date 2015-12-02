---
layout: landingpage
title: How it Works
icon: build
permalink: /2-HowItWorks/
categories:
 - home
---
Romana's multi-tenant cloud networking is based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in to the IP address adding application level context. By combining intelligent IP address management with route control and layer 3 firewalls on the hypervisor, network isolation is enforced between segments. 

Romana's intelligent IP Address Management (IPAM) system assigns IP addresses that capture application level context. This context is used to let Romana's host-based routing enforce tenant isolation, as well as any other t

based on the segment that it is part of, as well as the physical host where it is running. This, when deployed in a modern layer 3 routed access datacenter design, allows routes to be aggregated and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

Also, since routes are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion and policy based control is implemented by reconfiguring the default gateway on endpoint interfaces to steer traffic along specific paths to the IP address of the service endpoint.

More detail on how it works is available [here](/how/how/).

Or jump right to the topic you want to learn more about.

---

#### Topics

- [Routed Access Datacenter](/how/how/#routed-access-datacenter)   

- [Virtualization hosts](/how/how/#virtualization-hosts) 
 
- [VXLAN Tenant Isolation](/how/how/#vxlan-tenant-isolation)

- [Romana Tenant Isolation](/how/how/#romana-tenant-isolation)
  
- [IP Address Management](/how/how/#ip-address-managment)
  
- [Route Manager](/how/how/#route-manager) 

- [Microservices](/how/how/#microservices) 
 
- [Service Insertion](/how/how/#service-insertion) 
 
- [Policy Based Control](/how/how/#policy-based-control)  
---


