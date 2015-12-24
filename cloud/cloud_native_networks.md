---
layout: page
title: Cloud Native Networks
nav_text: Cloud Native SDN
menu_text: Cloud Native Networks
icon: cloud
firstnav: 1
secondnav: 1
permalink: /cloud/cloud_native_networks/

---

Building and operating scalable, high-performance, multi-tenant cloud computing networks is extremely difficult.  Today's preferred solution is to deploy a virtual network using VXLAN overlays and a commercial Enterprise SDN controller.

These overlay-based virtual networks were designed to address the challenges of deploying virtual machines on an enterprise network and are ill-suited to the challenges of the emerging container-based and Cloud Native architectural style. 

---

#### Cloud Native Architectural Style

*Cloud Native* is a term without a precise definition. The [Cloud Native Foundation](https://cncf.io/) avoids defining it at all, preferring to simply propose Kubernetes as the 'seed' technology for the architectural style. Here we use the term more broadly to include any newly developed application designed to run on popular IaaS platforms, such as [AWS]( http://aws.amazon.com/), [GCE]( https://cloud.google.com/compute/) or [Microsoft Azure](https://azure.microsoft.com/en-us/).

This is important from a networking perspective because these IaaS platforms have networks that are simple, and unlike traditional enterprise networks in many ways. Among other things, they only provide layer 3 connectivity and delegate responsibility for administering certain network services to application developers.  

For example, with AWS, each instance gets an IP address that can communicate with other instances, but it is left to the developer to manage access rights. Configuring and administering this kind of firewall and other networks services like Load Balancing is left to the developer.

Likewise, for Kubernetes, the requirement is only that each endpoint have a reachable [IP address](http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model). Kubernetes also assumes that endpoints are reachable by every other endpoint, leaving the isolation and multi-tenancy problems to the underlying IaaS platform.

Kubernetes also introduces the idea of a 'pod' and a 'service' to more naturally support [microservice](http://martinfowler.com/articles/microservices.html)-based applications. A pod is simply a collection of containers reachable by a single IP address, and a service is a collection of pods that all deliver the same function. Since each pod and service have a reachable IP address, they support client-side [Service Discovery]( http://microservices.io/patterns/client-side-discovery.html) using DNS and other [Service Discovery techniques]( https://dzone.com/articles/service-discovery-in-a-microservices-architecture).

This simple reachability model is important because it lets these networks support seamless and transparent scheduling of pods across cloud boundaries, greatly simplifying hybrid cloud deployment.

But unlike these simple cloud networks, OpenStack networking is extremely complicated because it also provides enterprise style networking for private clouds. However, one important feature it does provide for Cloud Native applications running in a private cloud is segment isolation for multi-tenancy.

Taken together, Romana defines *Cloud Native Networks* as networks that provide Amazon style connectivity, Kubernetes style reachability and service definitions and an OpenStack style tenancy model to allow isolated, private and public cloud deployment of Cloud Native applications.

---

#### Cloud Native SDN

In addition to supporting the Cloud Native architectural style, Cloud Native Networks must also accommodate the [exploding number of container endpoints and their shorter lifecycle]( http://events.linuxfoundation.org/sites/events/files/slides/cc15_mcguire.pdf). This requires automated provisioning via tight integration with cloud management systems such as Docker and Kubernetes.  

These unique requirements, along with the desire for more flexible, hybrid cloud deployment alternatives demand a fresh approach to building Cloud Native Networks. 

We call this new approach *Cloud Native SDN.*

Learn more about Cloud Native SDN and how it compares to other SDN designs [here](/cloud/cloud_native_sdn/)

