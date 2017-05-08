---
layout: post
title: Topology Aware IPAM Enables New Kubernetes Deployments in Cloud and on Premises 
categories:
- blog
permalink: /blog/topology-aware-IPAM/
---

Today we are happy to release Romana v1.6, which includes important new features including topology aware IP Address Management (TA-IPAM) for multi-network deployments. Romana v1.6 enables new OpenStack and Kubernetes deployment options in the datacenter and across EC2 VPC Availability Zones.

Topology aware IPAM is important because it lets operators capture network topology so that orchestration systems like OpenStack and Kubernetes have the flexibility they need to schedule pods and VMs anywhere on the network. Topology aware IPAM also maintains existing router configurations, which simplifies operations by eliminating the need to distribute new routes. 

Multi-network clusters are part of every high-availability configuration that split clusters across network availability zones (AZs). In addition, enterprises frequently have separate networks in their datacenter and want the flexibility to launch pods and VMs on the network that meets specific security or performance requirements.

![Multi-network Topologies]({{ site.baseurl }}/images/Networks.bmp)

Romana v1.6 with TA-IPAM is unique among Kubernetes network providers because it supports HA Kubernetes clusters across EC2 Availability Zones without an overlay network, delivering native performance and visibility. Large EC2 deployments are now possible as well, since Romana can be configured to avoid the 50 route VPC limit. 

Romana v1.6 also supports new egress traffic policies that let users block traffic to external endpoints. When used in AWS, egress fileting can be used to restrict pod access to AWS services without proxy filters or complex IAM. 

Romana enables the simplest, highest performing production-ready Kubernetes clusters in AWS by supporting:

* Native network performance and visibility 
* HA failover across AZs
* Kubernetes Network policy
* Egress filtering for AWS service isolation

More detail and examples of Kubernetes clusters split across EC2 Availability Zones, and how you can build them using kops is provided in this [post](/blog/multi-region-networks/). 

Romana v1.6 multi-network support for OpenStack means that Projects can launch VMs on independent Provider Networks. Kubernetes clusters can also use independent pod networks and launch pods on them through pod annotations that specify which network to use.

Details and examples of multi-network Kubernetes and OpenStack clusters are provide in the following [post](/blog/multi-region-networks/).

Romana v1.6 also has updated support for both [kops](https://github.com/kubernetes/kops) and [kargo]( https://github.com/kubernetes-incubator/kargo) for automated deployment of production Kubernetes cluster configurations.

Romana v1.6 is available today, [here](https://github.com/romana/romana) on GitHub. 
