---
layout: page
title: Romana Architecture
menu_text: Architecture
nav_text: NA
firstnav: 2
secondnav: 3
permalink: /how/romana_arch/
---

Romana is implemented as a collection of microservices that together, automate creation, administration and control of [Cloud Native Networks](/cloud/cloud_native_networks/). A brief description of each Service is provided below. For more detail, as well as a description of the Service interactions, please see the [Architecture Wiki](https://github.com/romana/core/wiki) on GitHub.

#### Cloud Orchestration

Romana integrates directly into Cloud Orchestration systems so that developers can continue to use their existing tools and techniques to build their applications. For OpenStack, this integration is through the Modular Layer 2 (ML2) and IPAM drivers.

#### Romana Services

Romana Cloud Native SDN is implemented using the following Services:

![Romana Architecture]({{ site.baseurl }}/images/arch.png)

- [Root Service](#root-service)
- [Authentication Service](#authorization-service)
- [IPAM Service](#ipam-service)
- [Topology Service](#topology-service)
- [Router Manager Service](#route-manager-service)
- [Host Configuration Service](#host-configuration-service)
- [Tenant Service](#tenant-service)
- [Storage Service](#storage-service)


> Release v0.6 does not implement the Authentication Service or the Route Manager Service

All Romana Services are written in [Go](https://golang.org/) and may be deployed all on a single server, or distributed across any number of systems. Romana Services may be replicated to scale performance or to provide redundancy for high availability. 


#### Root Service

The Root Service is where all Romana Services get registered, and where new Romana Services discover other Services and the overall capabilities of the environment. When a new service starts, it finds the Root Service and registers its name, IP address and capabilities. 

#### Authentication Service

The Authentication Service authenticates each request from Romana Services. Not fully implemented in Release v0.6.

#### IPAM Service

The Romana IPAM Service implements the layer 3 tenancy model as described [here](/how/romana_details/#ip-address-management/). It provides the proper IP address to OpenStack Nova after learning which host will launch the guest VM. The IPAM Service interacts with OpenStack via its IPAM API and the Romana OpenStack IPAM driver.

#### Topology Service

The Topology Service maintains the configuration of the physical network topology and the endpoints that are attached to the network.

#### Route Manager Service

*Not implemented in Release v0.6*

The Route Manager Service is responsible for updating all routes throughout the system, except for the initial default route set on endpoints, which is set locally via the Host Configuration Service

#### Host Configuration Service

The Host Configuration Service configures the gateway interface on the host so that the default routes on endpoints all use the same Romana gateway interface.


#### Tenant Service

The Tenant Service maintains its own list of tenants and the endpoints and segments that are associated with their projects. The Tenant Service provides this information to the IPAM Service when it needs to issue a new IP address for an endpoint.

#### Storage Service

The Romana Storage Service uses an SQL database as its primary data store for configuration and network state. When deployed with OpenStack, new database tables are created in the Neutron database and no separate data store is necessary.




