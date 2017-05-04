---
layout: post
title: Topology Aware IPAM Enables New Kubernetes Deployments in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.5, which includes important new features including topology aware IP Address Management (TA-IPAM) for multi-network deployments. Romana v1.5 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) providing new installation options on bare metal servers and across EC2 VPC Availability Zones.

Topology aware IPAM is important because it lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes have the flexibility they need to schedule pods and VMs anywhere on the network. Topology aware IPAM also maintains existing router configurations, eliminating the need to distribute new routes. 

Multi-network clusters are part of every high-availability configuration that split clusters across network availability zones as well as datacenter deployments on routed layer 3 networks. 

Romana v1.5 with TA-IPAM supports Kubernetes deployments across EC2 Availability Zones without an overlay network, delivering higher performance and visibility. Larger EC2 deployments are now possible as well, since Romana avoids the 50 VPC route limit. 

We believe Romana enables the simplest, highest performing Kubernetes clusters for EC2 configurations supporting:

* Clusters larger than 50 nodes
* Native network performance 
* Failover across AZs
* Namespace isolation via Kubernetes Network Policy APIs

More details on Kubernetes clusters across EC2 availability Zones, and how you can build them using kops is provided [here[(/blog/multi-region-networks/).

Romana v1.5 is available today, [here](https://github.com/romana/romana) on GitHub. 
