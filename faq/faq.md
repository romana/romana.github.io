---
layout: page
title: Frequently Asked Questions
menu_text: NA
nav_text: FAQ
icon: question_answer
firstnav: 4
secondnav: 9
permalink: /faq/
---

1. [How is Romana different from other container network alternatives like Flannel or Weave](#how-is-romana-different-from-flannel-or-weave)?
1. [How is Romana different from other layer 3 container network alternatives like Calico](#how-is-romana-different-from-calico)?
1. [What are the network requirements for Romana](#what-are-the-network-requirements-for-romana)?
1. [Does Romana support Kubernetes running on other public clouds](#does-romana-support-kubernetes-running-on-other-public-clouds)?
1. [Does Romana work with OpenStack](#does-romana-work-with-openstack)?
1. [Can I run Romana on my existing OpenStack underlay network](#can-i-run-romana-on-my-existing-openstack-underlay-network)?
1. [How are network segments isolated](#how-are-network-segments-isolated)?
1. [How can I build a Kubernetes cluster across VPC zones]()
1. [How does Romana avoid the 50 VPC route entry limtation in EC2]?
1. [Can I apply Kubernetes Network Policy across zones/subnets]?
1. [Does Romana work with Docker](#does-romana-work-with-docker)?

---

#### 1. How is Romana Different from Flannel or Weave?

[Flannel](https://coreos.com/flannel/docs/latest/) and [Weave](https://www.weave.works/products/weave-net/) create a local bridge on each container host and assign it a network address range. This lets local container endpoints communicate directly. Off-host traffic is encapsulated and tunneled across the physical network to the destination host. Other configurations are possible as well.

Both Flannel and Weave build overlay networks and encapsulate traffic between hosts, negatively affecting performance.

Romana does need an overlay and can deliver native network performance, even in VPCs, across zones. Also, Romana not bridge traffic so network policy can be applied at layer 3 with iptables rules on the host. Support for network policy APIs with flannel requires a separate network policy controller from Canal and Weave requires a network policy controller to run on every node.

---

#### 2. How is Romana Different from Calico?

Romana and Calico are similar in that they both use layer 3 for container networks. However, Romana is network agnostic and does not impose any specific technology or topology. Romana uses IP address management together with route advertisement to eliminate the need for an overlay network, even across VPC subnets.

Romana's topology-aware IPAM reduces the need for route updates when new endpoints are added and does not require full mesh peering of nodes.

---

#### 3. What are the network requirements for Romana?

Romana is network agnostic and does not impose any technology or topology restrictions. See [Datacenter Deployment Options](/deploy_romana/datacenter/) for details.

---

#### 4. Does Romana support Kubernetes running on other public clouds?

Yes. Romana will run on any Kubernetes cluster running public or private cloud. However, today, route advertisement for layer 3 networks is only available for AWS VPCs. This means that for other public clouds clusters will rely entirely on native cloud networking. 

---

#### 3. Does Romana work with OpenStack?

Yes. Romana can be used to build multi-tenant routed provider networks. See xxxx

---

#### 8. Will Romana run in AWS?

Yes. The current release supports both Kubernetes and OpenStack running on AWS EC2 instances. Current status [available here](/try_romana/installation/).

---

#### 9. How are network segments isolated?

Romana uses a new layer 3 based approach for tenant and segment isolation. Romana encodes tenant and segment identifiers directly in the IP address and Linux *iptables* rules are set to provide isolation. This enables multi-tenant cloud networks to be built without a virtual network overlay. See [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation) for  more detail.


