---
layout: page
title: Cloud Native SDN
nav_text: NA
menu_text: Cloud Native SDN
firstnav: 1
secondnav: 1
permalink: /cloud/cloud_native_sdn/

---
### Topics

- [Introduction](/cloud/cloud/#introduction)
- [Cloud Native Networks](/cloud/cloud/#cloud-native-networks)   
- [SDN Flavors](/cloud/cloud/#sdn-flavors)   
- [Cloud Native SDN](/cloud/cloud/#cloud-native-sdn)   

---

### Introduction

Romana is a new Software Defined Network (SDN) solution specifically designed for the [Cloud Native](https://cncf.io/) architectural style. The result of this focus is that Romana cloud networks are less expensive to build, easier to operate and deliver higher performance than cloud networks built using alternative SDN designs. 

We call Romana *Cloud Native* SDN because of this specific application focus, and also because it can be deployed in a cloud native way. Deployed wherever applications need to run, Romana will provide a uniformly reachable network that lets cloud management systems like Kubernetes seamlessly and transparently scale Cloud Native applications across private and public clouds worldwide.

{% include backtotopbutton.html %}
---

### Cloud Native Networks

*Cloud Native* is a term without a precise definition. The [Cloud Native Foundation](https://cncf.io/) avoids defining it at all, preferring to simply propose Kubernetes as the 'seed' technology for the architectural style. Here the term is used a little more broadly to include any newly developed application that is designed to run on popular IaaS platforms, such as [AWS]( http://aws.amazon.com/), [GCE]( https://cloud.google.com/compute/) or [OpenStack](http://www.openstack.org).

This is important from a networking perspective because these IaaS platforms have networks that are nothing like traditional enterprise networks. Among other things, they only provide layer 3 connectivity, do not support multicast. 

For example, with AWS, each instance gets an IP address that can communicate with other instances. It optionally may also get a public IP address. Beyond that, not much else.

Likewise, for Kubernetes, network requirements are simply that each [IP address be reachable](http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model). It also introduces the idea of a 'pod' and a 'service' to better support [microservice](http://martinfowler.com/articles/microservices.html) based applications. A pod is simply a collection of containers reachable by a single IP address, and a service is a collection of pods that all deliver the same function.

OpenStack networking on the other hand is extremely complicated because it attempts to also provide Enterprise style networking for private clouds. However, one important feature it can provide for Cloud Native applications in a private cloud is multi-tenancy.

Taken together, Romana defines Cloud Native networks as networks that provide Amazon style connectivity, Kubernetes style reachability and service definitions and an OpenStack style tenancy model to allow isolated, private and public cloud deployment of Cloud Native applications.

Kubernetes' simple reachability model has the added benefit of enabling Cloud Native networks to support seamless and transparent scheduling of pods across cloud boundaries, greatly simplifying hybrid cloud deployment.

{% include backtotopbutton.html %}

---

### SDN Flavors

It is easy to see how different flavors of SDN have emerged simply by looking at the objectives of their designs.

* *Enterprise SDN*: Virtualize Enterprise networks in their entirety, enabling datacenter and legacy applications to run unchanged in virtualized environments. The canonical example of Enterprise SDN is [VMware NSX](https://www.vmware.com/products/nsx). The defining characteristic of Enterprise SDN is the use of overlays and VTEPs to build isolated layer 2 virtual networks. Some implementations go further with efforts to virtualize many layer 3 and layer 4-7 functions.

* *Carrier SDN*: [Network Function Virtualization](http://searchsdn.techtarget.com/definition/network-functions-virtualization-NFV) (NFV) and automated service provisioning. [OPNFV]( https://www.opnfv.org/) is an industry wide effort to solve this problem with a purpose-built SDN controller and network orchestration platform. The defining characteristic of Carrier SDN is the ability to automate deployment of complex multi-tenant Service Functions in configurable chains, as part of a complete [Service Function Chaining Architecture](https://datatracker.ietf.org/doc/rfc7665/).

* *WAN SDN*: Classify and intelligently forward WAN traffic based on cost, performance and other policy constraints. Dynamically optimize WAN edge device configurations. Successful deployment results in large cost savings and is driving rapid adoption of [SDN solutions](http://www.networkcomputing.com/networking/software-defined-wan-a-primer/a/d-id/1307047) for the WAN.

* *Cloud Native SDN*: Integrate with cloud orchestration systems to automate provisioning of isolated multi-tenant [Cloud Native networks](#cloud-native-networks). Group cloud endpoints in to services, add load balancers as front-end service endpoints and make them discoverable (*Cloud endpoints* here simply means endpoints that reside anywhere in a public or private cloud).

Clearly, the objectives for Cloud Native SDN are vastly different from each of the other SDN designs and therefor a separate solution is warranted. 

Importantly, there is no requirement for layer 2 network features, traffic classification or device reconfiguration. Without these requirements, the single remaining challenge is provide to multi-tenancy. 

The complexity of building and operating VXLAN overlay networks for layer 2 virtualization are [well known](/how/#vxlan-isolation/), so dropping this from Cloud Native SDN immediately simplifies the design. However, without VXLAN, multi-tenancy needs to be implemented in some [other way](/how/details.html/#romana-tenant-isolation).

{% include backtotopbutton.html %}

---

### Cloud Native SDN

Cloud Native SDN can be simpler than Enterprise SDN because the requirements are much simpler. Specifically, endpoints do not require layer 2 adjacency, so the complexity of overlays and tunnel endpoints disappears. This also lets operators run their cloud networks directly on the physical devices, which delivers higher performance and provides better visibility, manageability and control.

Romana solves the multi-tenancy problem without overlays using an innovative [layer 3 tenancy model](/how/how/#romana-tenant-isolation) that lets operators deploy Romana in a [Routed Access](#/how/how/routed-access-datacenter) datacenter design, for even simpler operations. This tenancy model also captures [application level context](/how/how/#romana-tenant-isolation) directly in the network, simplifying the job of securing the network and applying policy based control.

Finally, since traffic runs on the physical network without encapsulation, all standard layer 3 traffic steering and filtering techniques can be used (in physical or virtual form factors) further simplifying network operations.

Learn more about how Romana Cloud Native SDN works [here](/how/romana/)

{% include backtotopbutton.html %}

---
