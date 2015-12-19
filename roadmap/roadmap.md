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

Release v0.1 is a complete re-implementation of the initial Proof of Concept (PoC) prototype we demonstrated earlier this year. There is still a lot to do before it will support anything beyond simple experimentation. 

That said, we are anxious to get the software out to users to give them some hands on experience with the software and to become familiar with the concepts behind [Cloud Native SDN](/cloud/cloud-native-sdn). In addition, there are many features we have not yet implemented and would like input prioritizing them, as well as help defining specific operational details.

The current v0.1 release *only* supports what was demonstrated to users with our PoC. Specifically:

- Deployment on EC2 with OpenStack
- Single Administrator Tenant
- Static Network Segments
- Default Segment Isolation Firewall
- Minimal Horizon integration

There will be continuous update to this release to fill gaps that enable more complete evaluation of the software. 

---

#### Release v0.2

The next major update to the software will include the following enhancements:

- Service Chaining and Insertion
- Expanded Policy Specification 
- Kubernetes Integration
- ???

---

#### Open Issues

Broadly speaking, the Roadmap includes the following functional enhancement. Follow the links to take you to the Github issue pages where the details are currently being discussed. 

- [Live Migration](#live-migration)
- [Topology Manager](#topology-manager)
- [Physical Device Management](#physical-device-management)
- [Security](#security)
- [Network Management Systems](#network-management-systems)

---

#### Live Migration

While Cloud Native application typically do not rely on live migration for either maintenance or to maintain availability, Operators, however, need the flexibility to manage their physical infrastructure. 

For this reason we are building a set of live migration features that let physical infrastructure to be managed more efficiently

{% include backtotopbutton.html %}

---

#### Hybrid Cloud

The Awesome Hybrid cloud feature 

{% include backtotopbutton.html %}

---

#### AWS IPSEC Gateway

Connecting to AWS requires that the IPSEC gateway to the VPC gateway be configured properly. We're working on that

{% include backtotopbutton.html %}

---


