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

2. [How is Romana different from other container network alternatives like Flannel, Weave and Calico](#how-is-romana-different-from-other-container-network-alternatives-like-flannel-or-weave)?
3. [Can I run Romana on a layer 2 network](#can-i-run-romana-on-a-layer-2-network)?
3. [Does Romana work with OpenStack](#does-romana-work-with-openstack)?
3. [Can I run Romana on my existing OpenStack underlay network](#can-i-run-romana-on-my-existing-openstack-underlay-network)?
4. [Does Romana work with Docker](#does-romana-work-with-docker)?
7. [How are network segments isolated](#how-are-network-segments-isolated)?
8. [How can I build a Kubernetes cluster across VPC zones]()
9. [How does Romana avoid the 50 VPC route entry limtation in EC2]?
10. [Can I apply Kubernetes Network Policy across zones/subnets]?
11. [Do you support other public clouds]?

---

#### 1. How is Romana Different from Flannel or Weave?

[Flannel](https://coreos.com/flannel/docs/latest/) and [Weave](https://www.weave.works/products/weave-net/) create a local bridge on each container host and assign it a network address range. This lets local container endpoints communicate directly. Off-host traffic is encapsulated and tunneled across the physical network to the destination host. Other configurations are possible as well.

Both Flannel and Weave are overlay networks and must encapsulate traffic between hosts, negatively affecting performance.

Romana does not bridge traffic so network policy can be applied at layer 3 with iptables rules running on the host. Support for network policy APIs with flannel requires a separate network policy controller from Canal Weave requires a network policy controller to run on every node.

---

#### 2. What kind of datacenter networks will Romana run on?

Romana is network agnostic and does not impose any technology or topology restrictions. See Datacenter Deployment Options for details.

{% include backtotopbutton.html %}

---

#### 3. Does Romana work with OpenStack?

Yes. Romana can be used to build multi-tenant routed provider networks. See xxxx

---

#### 8. Will Romana run in AWS?

Yes. The current release supports both Kubernetes and OpenStack running on AWS EC2 instances. Current status [available here](/try_romana/installation/).

---

#### 9. How are network segments isolated?

Romana uses a new layer 3 based approach for tenant and segment isolation. Romana encodes tenant and segment identifiers directly in the IP address and Linux *iptables* rules are set to provide isolation. This enables multi-tenant cloud networks to be built without a virtual network overlay. See [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation) for  more detail.


