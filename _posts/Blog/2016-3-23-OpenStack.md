---
layout: post
title: OpenStack IPAM APIs
categories:
- blog
permalink: /blog/openstack/
---

If you're going to the OpenStack Summit next month in Austin, please try to make it to the networking session on the new [OpenStack IPAM APIs](https://www.openstack.org/summit/austin-2016/summit-schedule/events/7078). Chris will be presenting our experience with these new APIs and how they are used with Romana to simplify OpenStack networking.

> Case Study: Neutron IPAM APIs and External IPAM Integration
Networking
<br><br>
Neutron IP Address Management (IPAM) APIs were first release in the Liberty Release. The session describes our experience developing an external IPAM system for OpenStack. The IPAM is part of Romana, a new open source Cloud Native SDN solution. IPAM is central to the way Romana creates multi-tenant networks without an overlay.
<br><br>
In this session, we will describe the hierarchical IP addressing scheme used by Romana for Cloud Native Networks and how it assigns addresses based on tenant, project and segment IDs to maintain the L3 heirarchy necessary for route aggregation. We also describe our IPAM driver design and the challenges we faced delaying address calculation until after the target Compute Node had been identified.
<br><br>
We will also demo OpenStack using Romana's IPAM when launching VMs across Nodes.
<br><br>
What can I expect to learn?
Attendees will learn how the new API behaves and how to avoid the early mistakes we made when developing and integrating our external IPAM.

Hope to see you there.
