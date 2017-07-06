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

Romana is a network and security automation solution for Cloud Native applications. Romana automates the creation of isolated Cloud Native Networks and secures applications using micro-segmentation and a distributed firewall that applies access control policies consistently across all endpoints and services, wherever they run.

Micro-segmentation of networks enable fine-grained network security policies to control traffic to and from applications. Romana uses familiar layer 3 network techniques to build secure, Cloud Native networks without a virtual network overlay.

Romana works in conjunction with cloud orchestration systems and issues IP addresses to VM and container endpoints through an [IP Address Management](/how/romana_details/) (IPAM) system. Each micro-segment is made up of one or more network CIDRs that define the segment boundary. Romana then installs routes to these segments on hosts and network devices so that they can forward traffic directly to endpoints and enforce network policy without the overhead of encapsulation.

Important advantages of this approach are that route aggregation makes route distribution unnecessary when new endpoints are added, and collapses the number of Linux *iptables* rules required for segment isolation.

Romana is network agnostic and runs on layer 2 LANs and VXLANs, layer 3 networks and public cloud networks like Amazon's VPC. New network advertisement features automate network configuration of top-of-rack devices as well as VPC route tables. 

For Kubernetes users, Romana is the only CNI provider that lets HA clusters split across zones apply policy to native VPC networks. 
Route aggregation avoids VPC route limitations, enabling native VPC networking on large clusters.


