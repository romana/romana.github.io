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


Romana is a network and security automation solution for cloud native applications. Romana automates the creation of isolated cloud native networks and secures applications using microsegmentation and enforces access control policies on all endpoints, wherever they run.

Microsegmentation of the network enables fine-grained access control policies to be applied to all application traffic. Romana uses familiar layer 3 network techniques to build secure, cloud native networks without a virtual network overlay.

Romana works in conjunction with cloud orchestration systems and issues IP addresses using an intelligent, topology-aware IP Address Management (IPAM) system. Each microsegment is made up of one or more network CIDRs that define the segment boundary. Romana then installs routes to these segments on hosts and network devices so that they can forward traffic directly to endpoints and enforce network policy without the overhead of encapsulation. 

Route aggregation makes route distribution unnecessary when new endpoints are added and collapses the number of Linux *iptables* rules required for segment isolation. 

Romana is network agnostic and runs on layer 2 LANs and VLANs as well as IP fabrics running VXLANs, layer 3 networks and public cloud networks like Amazon's VPC. New network advertisement features automate network configuration of top-of-rack devices as well as VPC route tables.

For Kubernetes users, Romana is the only CNI provider that lets HA clusters split across zones apply policy to native VPC networks. Route aggregation avoids VPC route limitations, enabling native VPC networking on large clusters.
