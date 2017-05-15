---
layout: post
title: Topology Aware IPAM Enables New Kubernetes Deployments in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.6, which includes the first of several important architectural advances to increase deployment flexibility and simplify operations. 

Fundamental to Romana is intelligent IP address management that lets real network CIDRs define isolation boundaries.  This has numerous advantages including eliminating overlays and the requirement for route distribution as well as simplifying security enforcement. 

However, Romana v1.0 IPAM is unable to optimize IP address allocation, which results in inefficient use of IP addresses in the datacenter where they can be scarce and complicates AWS VPC deployments across subnets. Simple IPAM also limits HA configurations in EC2 to 50 nodes or require an overlay.

Romana v1.6 with Topology Aware IP Address Management (TA-IPAM) solves both of these problems.

which includes important new features including topology aware IP Address Management (TA-IPAM) for multi-network deployments. Romana v1.6 enables new OpenStack and Kubernetes deployment options in the datacenter and across EC2 VPC Availability Zones, all without an overlay network.

Topology aware IPAM is important because it lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes have the flexibility they need to schedule pods and VMs anywhere on the network. Topology aware IPAM also maintains existing router configurations, which simplifies operations by eliminating the need to distribute new routes. 

Multi-network clusters are necessary for high-availability configurations split across network availability zones (AZs). In addition, enterprises frequently have separate networks in their datacenter and want the flexibility to launch pods and VMs on the network that meets specific security or performance requirements.

![Multi-network Topologies]({{ site.baseurl }}/images/Networks.png)

Romana v1.6 with TA-IPAM is unique among Kubernetes network providers because it supports HA Kubernetes clusters across EC2 Availability Zones without an overlay network, delivering native performance and visibility. Large EC2 deployments without an overlay are now possible as well, since Romana uses route aggregation to always remain within VCP's 50 route limit. 

Romana v1.6 also supports new egress traffic policies that let users block traffic to external endpoints. When used in AWS, egress filtering controls pod access to other AWS services (i.e. RDS, etc.) without proxy filters or complex IAM. 

Romana enables the simplest, highest performing production-ready Kubernetes clusters in AWS by supporting:

* Native network performance and visibility
* Highly available, multi-master configurations across AZs
* VPC Route aggregation for large clusters 
* Kubernetes Network Policy APIs for pod isolation
* Egress filtering for AWS service isolation

Details on how to deploy Kubernetes clusters split across EC2 Availability Zones, and how you can build them using kops is provided in this [post](/blog/multi-zone-networks/). 

Romana v1.6 multi-network support for OpenStack means that users can launch VMs on independent Provider Networks. Kubernetes clusters can also use independent pod networks and launch pods on them through pod annotations that specify which network to use.

Details and examples of multi-network Kubernetes and OpenStack clusters are available [here](/blog/multi-network-deployments/).

Romana v1.6 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) for automated deployment of production Kubernetes cluster configurations in the datacenter and AWS.

Romana v1.6 is available today, [here](https://github.com/romana/romana) on GitHub. 
