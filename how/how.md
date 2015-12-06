---
layout: page
title: How it Works
icon: build
firstnav: 2
secondnav: 0
---

#### tl;dr

Romana's multi-tenant cloud networking is based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in the IP address. 

Romana includes an intelligent IP Address Management (IPAM) system that assigns IP addresses that capture network topology and application level context. This lets Romana take advantage of modern, layer 3 routed access datacenter designs, and allows routes to be aggregated and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

A Route Manager configures new routes on the hypervisor and uses the application level context in the addressing to simplify configuration of firewall rules for tenant isolation and other traffic management policies. 

Also, since routes on network devices are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion and policy based control is implemented by reconfiguring the default gateway on endpoint interfaces to steer traffic along specific paths to the IP address of the service endpoint.

More details on how it works are available [here](/how/howitworks.html/).

Or jump right to the topic you want to learn more about.

---

#### Topics

- [Introduction](/how/howitworks.html/#introduction)
- [Routed Access Datacenter](/how/howitworks.html #routed-access-datacenter)   
- [Virtualization hosts](/how/howitworks.html/#virtualization-hosts) 
- [VXLAN Tenant Isolation](/how/howitworks.html/#vxlan-tenant-isolation)
- [Romana Tenant Isolation](/how/howitworks.html/#romana-tenant-isolation)
- [IP Address Management](/how/howitworks.html/#ip-address-managment)
- [Route Manager](/how/howitworks.html/#route-manager) 
- [Microservices](/how/howitworks.html/#microservices) 
- [Service Insertion](/how/howitworks.html/#service-insertion) 
- [Policy Based Control](/how/howitworks.html/#policy-based-control)  

---

