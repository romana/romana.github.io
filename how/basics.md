---
layout: page
title: Just the Basics
nav_text: How it Works
menu_text: Basics
icon: build
firstnav: 1
secondnav: 1
permalink: /how/romana_basics/
---

Romana creates isolated Cloud Native networks and applies security and network policy to them using standard layer 3 networking techniques. Just like networks on the public internet, isolation and access is based on IP address ranges that identify what traffic is allowed and where it can flow. Romana uses these familiar layer 3 techniques to build secure, Cloud Native networks.

Romana controls IP addresses on VM and container endpoints with an IP Address Management (IPAM) system that lets familiar network CIDR address ranges become the unit of isolation. Romana then installs routes on hosts so that the Linux kernel can forward traffic directly to endpoints and enforce network policy without the overhead of encapsulation.

An important benefit of this approach is that route aggregation makes additional route distribution unnecessary and collapses the number of Linux *iptables* rules required for segment isolation.

Romana captures network topology so that its [topology aware IPAM]/how/romana_details/) issues addresses that are reachable via existing routes on switched (layer 2) and routed (layer 3) datacenter networks and between EC2 VPC Availability Zones, avoiding the complexity and overhead of tunnels and overlay networks. 

More details on how Romana works are available [here](/how/romana_details/).
