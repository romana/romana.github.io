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

#### Release v0.1: Proof of Concept re-implemented

Release v0.1 is a complete re-implementation of the initial Proof of Concept (PoC) prototype we demonstrated earlier this year. There is still a lot to do before it will support anything beyond simple, small scale experimentation. 

That said, we are anxious to get the software out to users to let them get hands-on experience with the software and to become familiar with the concepts behind [Cloud Native SDN](/cloud/cloud-native-sdn). In addition, there are many features we have not yet implemented and would like input prioritizing them, as well as help defining specific operational details.

The current v0.1 release supports *only* what was demonstrated to users with the original PoC. Specifically:

- Deployment on EC2 with OpenStack
- Single *Administrator* Tenant
- Pre-defined Network Segments
- Default Segment Isolation Firewall
- Minimal Horizon integration

See the [README](http://www.github.com/romana/romana/README.md) file in the repository for more detail. 

We will quickly update this release to include a CLI to more easily add Tenants and Segments, as well as a Vagrant based laptop deployment alternative.

---

#### Release v0.2: Service Chaining and Insertion Prototype

The next update will include the following enhancements:

- Prototype Service Chaining and Insertion
- Expanded Policy Specification 
- Basic Kubernetes Integration
- Improved OpenStack Horizon Integration

An introduction to how Romana implements Service Insertion is available [here](/how/romana_details/#service-insertion).

---
