---
layout: post
title: Topology Aware IPAM Enables New Kubernetes Deployments in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v2.0, which includes important architectural advances to increase deployment flexibility and simplify operations. 

Fundamental to Romana is intelligent IP address management that lets real network CIDRs define isolation boundaries.  This has numerous advantages including eliminating overlays and using route aggregation to minimize network configuration updates, which enables higher performance and simpler, more secure operations. 

However, Romana v1.0 IPAM provided only very simple IP address allocation, which results in inefficient use of addresses. This is a problem in the datacenter where addresses may be scarce and complicates deployment across subnets. 

Romana v2.0 with Topology Aware IP Address Management (TA-IPAM) and route advertisement support solves both of these problems.

Topology aware IPAM lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes have the flexibility they need to schedule pods and VMs anywhere on the network, even across different subnets. A single, aggregated route advertised from hosts configures network devices and topology aware IPAM ensures that new endpoints are reachable via these existing routes.

Romana v2.0 works on both layer 2 and layer 3 networks and does not rely on any vendor specific IP fabric or route distribution protocol enabling the widest range of deployment options across datacenters and the public cloud.

### Multi-network support

Multi-network clusters are necessary for high-availability configurations split across network availability zones (AZs). In addition, enterprises frequently have separate networks in their datacenter and want the flexibility to launch pods and VMs on the network that meets specific security or performance requirements.

![Multi-network Topologies]({{ site.baseurl }}/images/Networks.png)

Romana v2.0 with TA-IPAM is unique among Kubernetes network providers because it supports HA Kubernetes clusters across EC2 Availability Zones without an overlay network, delivering native performance and visibility. Large EC2 deployments without an overlay are now possible as well since Romana uses route aggregation to always remain within VCP's 50-route limit. 

Romana v2.0 also supports new egress traffic policies that let users block traffic to external endpoints. When used in AWS, egress filtering controls pod access to other AWS services (i.e. RDS, etc.) without proxy filters or complex IAM. 

Romana enables the simplest, highest performing production-ready Kubernetes clusters in AWS by supporting:

* Native network performance and visibility
* Highly available, multi-master configurations across AZs
* VPC Route aggregation for large clusters 
* Kubernetes Network Policy APIs for pod isolation
* Egress filtering for AWS service isolation

Details on how to deploy multi-master/HA Kubernetes clusters split across EC2 Availability Zones, without an overlay is provided in this [post](/blog/multi-zone-networks/). 

Romana v2.0 multi-network support for OpenStack means that users can launch VMs on independent Provider Networks. Kubernetes clusters can also use independent pod networks and launch pods on them through pod annotations that specify which network to use.

Together, the new IPAM and route advertisement features now available in Romana v2.0 enable a variety of new configuration options for datacenter deployment and across the public cloud.

Since there are lots of details to dig into for how these new features work, we're providing a separate blog post for the most the common deployment scenarios, including:

* [Layer 2 Network Deployment](/blog/multi-network-deployments/)
* [Layer 3 Network Deployment](/blog/multi-network-deployments/) 
* [Multi-zone VPC Deployment](/blog/multi-network-deployments/)
* [OpenStack Provider Network Deployment](/blog/multi-network-deployments/)

Romana v2.0 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) for automated deployment of production Kubernetes cluster configurations in the datacenter and AWS.

Romana v2.0 is available today, [here](https://github.com/romana/romana) on GitHub. 
