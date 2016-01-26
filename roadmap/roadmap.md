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

See the Romana GitHub [Wiki](https://github.com/romana/romana/wiki/Roadmap) page for the most up to date details on the Roadmap. Highlights of the current and upcoming release are included below.

#### Release v0.6.x: Basic Cloud Native Networks

Release v0.6.x is a re-implementation of the initial Proof of Concept (PoC) prototype that was demonstrated earlier this year, but built with the new [microservices architecture](/how/romana_arch/).

We are anxious to get the software out to users to let them get hands-on experience with the software and to become familiar with the concepts behind [Cloud Native SDN](/cloud/cloud_native_sdn). In addition, there are certain features we have not yet implemented and would like input prioritizing them, as well as help defining specific operational details.

The current v0.6.2 release supports basic Layer 3 Cloud Native Networks for OpenStack tenants. This release supports:

- Deployment on EC2 with up to 4 OpenStack Compute Nodes
- Two default Tenants: *admin* and *demo*
- Two Pre-defined Network Segments per Tenant, s1 and s2
- Default Segment Isolation Firewall
- Minimal command line configuration capabilities
- Minimal Horizon integration

See the [README](http://www.github.com/romana/romana/) file in the repository for details on how to set up your own OpenStack cluster running Romana and what you can do with it. 
