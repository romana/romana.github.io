---
layout: post
title: How it Works
icon: build
permalink: /2-HowItWorks/
firstnav: 2
secondnav: 0
categories:
 - home
---

#### tl;dr

Romana's multi-tenant cloud networking is based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in the IP address. 

Romana includes an intelligent IP Address Management (IPAM) system that assigns IP addresses that capture network topology and application level context. This lets Romana take advantage of modern, layer 3 routed access datacenter designs, and allows routes to be aggregated and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

A Route Manager configures new routes on the hypervisor and uses the application level context in the addressing to simplify configuration of firewall rules for tenant isolation and other traffic management policies. 

Also, since routes on network devices are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion and policy based control is implemented by reconfiguring the default gateway on endpoint interfaces to steer traffic along specific paths to the IP address of the service endpoint.

More details on how it works are available [here](/how/how/).

Or jump right to the topic you want to learn more about.

---

#### Topics

- [Introduction](/how/how/#introduction)
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

