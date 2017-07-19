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
1. [Will Romana run in AWS](#will-romana-run-in-aws)?
1. [Does Romana support Kubernetes running on other public clouds](#does-romana-support-kubernetes-running-on-other-public-clouds)?
1. [Does Romana work with OpenStack](#does-romana-work-with-openstack)?
1. [How are network segments isolated](#how-are-network-segments-isolated)?

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

Romana is network agnostic and does not impose any technology or topology restrictions. Romana works on standard layer 2 networks including simple LANs as well as VLANs and VXLAN overlay networks. Routed network designs are also supported. See [Datacenter Deployment Options](/deploy_romana/datacenter/) for details.

---

#### 4. Will Romana run in AWS?

Yes. Romana supports [kops](https://github.com/kubernetes/kops), the popular AWS installation and operations project for Kubernetes. Romana allows Kubernetes clusters to use native VPC networking across availability zones. Romana avoids the VPC route limitations by aggregating routes to stay within the 50 route. More details [here](/deploy_romana/public_cloud/)


---

#### 5. Does Romana support Kubernetes on other public clouds?

Yes. Romana will run on public cloud. However, today, route advertisement for layer 3 networks is only available for AWS VPCs. This means that for other public clouds clusters will use only inter-node routes. More details [here]/deploy_romana/public_cloud/

---

#### 6. Does Romana work with OpenStack?

Yes. Romana lets users build multi-tenant networks on routed provider networks with standard Neutron. See [this blog post](/blog/routed-provider-networks/) for more detail.

---

#### 7. How are network segments isolated?

Romana isolates endpoints with traffic filters applied to network CIDRs. Network interfaces tap directly into the kernel providing isolation from other endpoints on the host that might otherwise share a linux bridge where Ethernet frames flow freely. This lets packet filtering at layer 3 using iptable rules enforce isolation and network security policy.
