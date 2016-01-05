---
layout: page
title: Roadmap
menu_text: Roadmap
nav_text: Roadmap
icon: map
firstnav: 6
secondnav: 9
permalink: /roadmap/
---

#### Release v0.6: Basic Cloud Native Networks

Release v0.6 is a re-implementation of the initial Proof of Concept (PoC) prototype that was demonstrated earlier this year, but built with the new [microservices architecture](/how/romana_arch/).

We are anxious to get the software out to users to let them get hands-on experience with the software and to become familiar with the concepts behind [Cloud Native SDN](/cloud/cloud_native_sdn). In addition, there are certain features we have not yet implemented and would like input prioritizing them, as well as help defining specific operational details.

The current v0.6 release supports basic Layer 3 Cloud Native Networks for OpenStack tenants. The:

- Deployment on EC2 with up to 4 OpenStack Compute Nodes
- Two default Tenants: *admin* and *demo*
- Two Pre-defined Network Segments per Tenant, s1 and s2
- Default Segment Isolation Firewall
- Minimal Horizon integration

See the [README](http://www.github.com/romana/romana/) file in the repository and the [Wiki]( http://www.github.com/romana/romana/wiki) home page for details on how to set up your own OpenStack cluster running Romana and what you can do with it. 

We will quickly update this release to include a CLI to more easily add Tenants and Segments, as well as a Vagrant based laptop deployment alternative.

---

#### Release v0.7: Service Chaining and Insertion

The next update will include the following enhancements:

- Prototype Service Chaining and Insertion
- Expanded Policy Specification 
- Basic Kubernetes Integration
- Improved OpenStack Horizon Integration

An introduction to how Romana implements Service Insertion is available [here](/how/romana_details/#service-insertion).
