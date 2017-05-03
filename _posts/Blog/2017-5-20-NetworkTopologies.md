---
layout: post
title: Topology Aware IPAM Enables New Kubernetes Deployments in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.5, which includes important new features including topology aware IP Address Management (TA-IPAM) for multi-network deployments. Romana v1.5 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) providing new installation options on bare metal servers and across EC2 VPC Availability Zones.

Topology aware IPAM is important because it lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes can schedule pods and VMs across multiple networks, without the need to update routers. 

Multi-network clusters are part of every high-availability configuration that split clusters across network availability zones as well as datacenter deployments on routed layer 3 networks. 

Romana v1.5 with TA-IPAM now supports Kubernetes deployments across EC2 Availability Zones. We believe Romana now enables the simplest, highest performing HA configurations for EC2 deployment:

* Support network isolation via Kubernetes Network Policy APIs
* Avoid the 50 VPC route limit
* Requires no tunnels or encapsulation
* No BGP or route distribution software

Romana v1.5 is available today, [here](https://github.com/romana/romana) on GitHub. 
