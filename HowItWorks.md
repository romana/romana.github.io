---
layout: landingpage
title: How it Works
icon: build
permalink: /2-HowItWorks/
categories:
 - home
---
## How it Works

Romana multi-tenant cloud networking is based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in to the IP address. By combining intelligent IP address management with route control and layer 3 firewalls on the hypervisor, network isolation is enforced between segments. 

Romana's intelligent IP Address Management (IPAM) system assigns endpoint IP addresses based on the segment that it is part of, as well as the physical host where it is running. This, when deployed in a modern layer 3 routed access datacenter design, allows routes to be aggregated and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

Also, since routes are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion is implemented by reconfiguring the default gateway on tenant endpoints to the IP address of the service endpoint.

Further details on how it works are available [here](/how/README/).

Or jump right to the topic you want to learn more about.

### Topics

- [Routed Access Datacenter](/how/README/#routed-access-datacenter)   

- [Virtualization hosts](/how/README/#virtualization-hosts) 
 
- [VXLAN Tenant Isolation](/how/README/#vxlan-tenant-isolation)

- [Romana Tenant Isolation](/how/README/#romana-tenant-isolation)
  
- [IP Address Management](/how/README/#ip-address-managment)
  
- [Route Manager](/how/README/#route-manager) 
 
- [Service Insertion](/how/README/#service-insertion) 
 
- [Policy Based Control](/how/README/#policy-based-control)  

---


