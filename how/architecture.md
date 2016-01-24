---
layout: page
title: Romana Architecture
menu_text: Architecture
nav_text: NA
firstnav: 2
secondnav: 3
permalink: /how/romana_arch/
---

Romana is implemented as a collection of microservices that together automate
creation, administration and control of [Cloud Native
Networks](/cloud/cloud_native_networks/). A brief description of each service
is provided below. For more detail, as well as a description of the service
interactions, please see the [Architecture
Wiki](https://github.com/romana/core/wiki) on GitHub.

#### Cloud Orchestration

Romana integrates directly into Cloud Orchestration systems so that developers
can continue to use their existing tools and techniques to build their
applications. For OpenStack, this integration is through the Modular Layer 2
(ML2) and IPAM drivers. For Kubernetes, a CNI plugin is provided.

#### Romana Services

The Romana Cloud Native SDN is implemented using the following services:

![Romana Architecture]({{ site.baseurl }}/images/arch.png)

- [Root Service](#root-service)
- [Authentication Service](#authorization-service)
- [IPAM Service](#ipam-service)
- [Topology Service](#topology-service)
- [Route Manager Service](#route-manager-service)
- [Tenant Service](#tenant-service)
- [Romana Agents](#romana-agents)


> Release v0.6 does not implement the Authentication service or the Route Manager Service

All Romana services are written in [Go](https://golang.org/) and may be
deployed all on a single server, or distributed across any number of systems.
Romana services may be replicated to scale performance or to provide redundancy
for high availability. 


#### Root Service

The Root Service is where all Romana services are registered and discover other
services as well as the the overall capabilities of the environment. When a new
service starts, it finds the Root Service and registers its name, IP address
and capabilities. 

#### Authentication Service

The Authentication Service is used for clients and services to prove their
identity and to obtain authentication tokens.  Not fully implemented in Release
v0.6.

#### IPAM Service

The Romana IPAM Service implements the layer 3 tenancy model as described
[here](/how/romana_details/#ip-address-management/). It integrates with
OpenStack via a pluggable IPAM driver: The request for the IP address is
issued after the Nova scheduler decided on which host to place a new endpoint.
For Kubernetes the integration takes place via the CNI plugin.

#### Topology Service

The Topology Service maintains the configuration of the physical network
topology and the endpoints that are attached to the network.

#### Route Manager Service

*Not implemented in Release v0.6*

The Route Manager Service is responsible for updating all routes throughout the
system, except for the initial default route set on endpoints, which is set
locally via the Romana Agents.

#### Tenant Service

The Tenant Service maintains its own list of tenants, which can be created to
map tenants from the cloud orchestration system, such as OpenStack. When the
IPAM Service needs to issue a new IP address for an endpoint it may query the
Tenant Service for information.

#### Romana Agents

The Romana Agents configure the Romana gateway interface on the hosts for use
as default gateway for any Romana managed endpoints. They also set the
necessary routes to reach Romana addresses on other hosts.  Furthermore, they
are used to configure traffic policy rules, for example security rules for
traffic isolation.

