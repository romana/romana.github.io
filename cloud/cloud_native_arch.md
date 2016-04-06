---
layout: page
title: Cloud Native Architectural Style 
nav_text: Cloud Native SDN
menu_text: Cloud Native Architecture
icon: cloud
firstnav: 3
secondnav: 3
permalink: /cloud/cloud_native_arch/

---

*Cloud Native* is a term without a precise definition. The [Cloud Native Foundation](https://cncf.io/) avoids defining it at all, preferring to simply propose Kubernetes as the 'seed' technology for the architectural style. Here we use the term more broadly to include any newly developed application designed to run on popular IaaS platforms, such as [AWS]( http://aws.amazon.com/), [GCE]( https://cloud.google.com/compute/) or [Microsoft Azure](https://azure.microsoft.com/en-us/).

This is important from a networking perspective because these IaaS platforms have networks that are simple and unlike traditional enterprise networks in many ways. Among other things, they only provide layer 3 connectivity and delegate responsibility for administering certain network services to application developers.  

For example, with AWS, each instance gets an IP address that can communicate with other instances, but it is left to the developer to manage access by defining Security Groups with specific network policies and applying them directly to endpoints. Configuring and administering this kind of firewall and other networks services like Load Balancing is also left to the developer.

Likewise, for Kubernetes, the requirement is only that each endpoint have a reachable [IP address](http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model). Kubernetes also assumes that endpoints are reachable by every other endpoint, leaving the access control policy (i.e. isolation and multi-tenancy) to the underlying IaaS platform.

Kubernetes also introduces the idea of a 'pod' and a 'service' to more naturally support [microservice](http://martinfowler.com/articles/microservices.html) based applications. A pod is simply a collection of containers reachable by a single IP address, and a service is a collection of pods that all deliver the same function. Since each pod and service have a reachable IP address, they support client-side [Service Discovery]( http://microservices.io/patterns/client-side-discovery.html) using DNS and other [Service Discovery techniques]( https://dzone.com/articles/service-discovery-in-a-microservices-architecture).

Kubernetes' simple reachability model is important because it avoids the complexity of extending layer 2 networks across the datacenter (and into the cloud) and enables seamless and transparent scheduling of pods across cloud boundaries, greatly simplifying hybrid cloud deployment.

Unlike these simple cloud networks, OpenStack networking is often very complicated because it also provides enterprise style networking for private clouds. However, one important feature it does provide for Cloud Native applications running in a private cloud is multi-tenancy and network segment isolation for policy-based control. 

With the release of Kubernetes v1.3 a new Network Policy API will be available that supports network isolation for multi-tier applications. This is the first step toward a more complete multi-tenant capability.

*Cloud Native Networks*, therefore, are networks that provide Amazon and Kubernetes style reachability and service definitions, plus support isolation and multi-tenancy that can be deployed securely in public and private clouds worldwide. 
