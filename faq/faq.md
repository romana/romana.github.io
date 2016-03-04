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

1. [What does Cloud Native mean](#what-does-cloud-native-mean)?
2. [What is Cloud Native SDN](#what-is-cloud-native-sdn)?
2. [How is Romana different from other layer 3 based SDN alternatives](#how-is-romana-different-from-other-layer-3-based-sdn-solutions)?
2. [How is Romana different from other container network alternatives like Flannel or Weave](#how-is-romana-different-from-other-container-network-alternatives-like-flannel-or-weave)?
3. [Can I run Romana on a layer 2 network](#can-i-run-romana-on-a-layer-2-network)?
3. [Does Romana work with OpenStack](#does-romana-work-with-openstack)?
3. [Can I run Romana on my existing OpenStack underlay network](#can-i-run-romana-on-my-existing-openstack-underlay-network)?
4. [Does Romana work with Docker](#does-romana-work-with-docker)?
5. [Does Romana work with Kubernetes](#does-romana-work-with-kubernetes)?
6. [Will Romana run in AWS](#will-romana-run-in-aws)?
7. [How are network segments isolated](#how-are-network-segments-isolated)?
8. [Does it have any layer 2 semantics](#does-it-have-any-layer-2-semantics)?
9. [How does it scale](#how-does-it-scale)?
10. [Will I run out of IPv4 Addresses](#will-i-run-out-of-ipv4-addresses)?
10. [What are the plans for IPv6](#what-are-the-plans-for-ipv6)?
11. [Does Romana support overlapping IP addresses](#does-romana-support-overlapping-ip-addresses)?
12. [How does Romana handle NAT](#how-does-romana-handle-nat)?
13. [What kind of performance gains can I expect](#what-kind-of-performance-gains-can-i-expect)?
14. [Is there any training available](#is-there-any-training-available)?

---

#### 1. What does Cloud Native mean?

Cloud Native is a term without a precise definition, but is generally understood to be the name for applications that are developed specifically for deployment in the cloud. 

The [Cloud Native Foundation](https://cncf.io/) avoids defining it at all, preferring to simply propose Kubernetes as the 'seed' technology for the architectural style. Romana uses the term more broadly to include any newly developed application designed to run on popular IaaS platforms, such as [AWS]( http://aws.amazon.com/), [GCE]( https://cloud.google.com/compute/) or [Microsoft Azure](https://azure.microsoft.com/en-us/).  See [Cloud Native Networks](/cloud/cloud_native_networks/) for more details.

{% include backtotopbutton.html %}

---

#### 2. What is Cloud Native SDN?

Cloud Native SDN is a specific kind of Software Defined Networking approach focused on building and operating Cloud Native Networks. Cloud Native Networks do not need layer 2 networks so can be built without an virtual network overlay. Romana Cloud Native SDN lets operators build Cloud Native Networks that are easier to deploy and maintain and can deliver higher performance that cloud networks build using alternative SDN techniques. 

See [Cloud Native SDN](/cloud/cloud_native_sdn) for more details.

{% include backtotopbutton.html %}

---

#### 3. How is Romana Different from other Layer 3 Based SDN solutions?

There are several SDN solutions available today that rely on layer 3 networks to varying degrees. Among them are [OpenContrail](http://www.opencontrail.org), [Calico](http://www.projectcalico.org), and [Nuage Networks]( http://www.nuagenetworks.net/).  While each of these solutions provide similar layer 3 functionality, there are important operational differences.

OpenContrail uses host based virtual routers [(vRouters)]( https://github.com/Juniper/contrail-vrouter) to maintain VRFS and MPLS to isolate networks. vRouters get updated via XMPP and operators can optionally run an VXLAN overlay to provide layer 2 connectivity. Romana does not use host based router, it uses the routing capabilities of the Linux kernel and does not require MPLS for network multitanancy.

Nuage Networks also uses a host based vRouter, but uses BGP-MP for route distribution. 

Calico uses the Linux host as a router, but requires BGP for route distribution. Also, Calico deployment presumes compute hosts are all on a single L2 network and ARP to learn about the other routers in the configuration. 

When a new endpoint is added with Calico, a new host route (/32 CIDR) is added on the local host. This /32 route is then propagated to all the other hosts via BGP running on the host. 

Romana on the other hand lets hosts run on either a layer 2 or layer 3 network and leverages route aggregation to avoid the need for a route distribution protocol.

Also, Calico requires a key value store (KVS) to maintain configuration state for the network. Romana does not need a KVS.

In summary the main differences are:

|  | Romana| OpenContrail | Nuage | Calico |
| Network | Layer 3 | Layer 3 | Layer 3 | Layer 2 |
| Route Distribution | Static | MXPP | BGP-MP | BGP |
| Configuration State | DB | DB | DB | KVS |
{: class='romanatable'}


{% include backtotopbutton.html %}

---

#### 4. How is Romana Different from other container network alternatives like Flannel or Weave?

[Flannel](https://coreos.com/flannel/docs/latest/) is the open source overlay virtual network included in CoreOS. Flannel creates a local bridge on each container host and assign it a network address range. This lets local container endpoints sit on a single layer 2 network where they can communicate directly. Off-host traffic is encapsulated and tunneled across the physical network to the destination host where it is forwarded to the local container bridge.

Flannel also has a 'host gateway' option for use on physical switched layer 2 networks to forward traffic directly to the destination without encapsulation.

[Weave](https://www.weave.works/products/weave-net/) is another overlay virtual network available from Weaveworks.  Weave lets users create tunnels between container hosts to build layer 2 overlay virtual networks. 

Since both Flannel and Weave are overlay networks the host CPU must perform encapsulation of all traffic. The disadvantages of overlay networks are [outlined here](/how/background/#vxlan-tenant-isolation). 

Also, neither Weave nor Flannel will support [Kubernetes network policy functions](/blog/MeetupDemo/).

{% include backtotopbutton.html %}

---

#### 5. Can I run Romana on a layer 2 network?

Yes. Romana will work on simple switched layer 2 networks, layer 2 fabrics, routed layer 3 networks and even on public cloud networks like Amazon's Virtual Private Cloud.

When Romana runs on layer 2 networks, the Route Manager will configure routes on each host to every other host.

{% include backtotopbutton.html %}

---

#### 6. Does Romana work with OpenStack?

Yes. Romana works with cloud orchestration systems like OpenStack to launch VM and container endpoints. Romana integrates with OpenStack through a Modular Layer 2 device driver and an interface to its new IPAM API.

You can try Romana today with OpenStack running on Amazon EC2 instances. See our [current release](/try_romana/openstack/) for details.

See current [Roadmap](/roadmap/) for details.


{% include backtotopbutton.html %}

---

#### 7. Can I run Romana on my existing OpenStack underlay network?

Soon. The current release does not support this, but we plan to in the future. If you would like to contribute to this effort, please let us know.

See current [Roadmap](/roadmap/) for details.


{% include backtotopbutton.html %}

---

#### 8. Does Romana work with Docker?

It will. The current [v0.6 release](/try_romana/docker/) does not support Docker, but it is planned for a future release.

{% include backtotopbutton.html %}

---

#### 9. Does Romana work with Kubernetes?

We are working on support for the Container Network Interface (CNI) in Kubernetes and it will be available soon. Current status [available here](/try_romana/kubernetes/).

{% include backtotopbutton.html %}
---

#### 10. Will Romana run in AWS?

Yes. The current release supports OpenStack running on AWS EC2 instances. Current status [available here](/try_romana/openstack/).

{% include backtotopbutton.html %}

---

#### 11. How are network segments isolated?

Romana uses a new layer 3 based approach for tenant and segment isolation. Romana encodes tenant and segment identifiers directly in the IP address and Linux *iptables* rules are set to provide isolation. This enables multi-tenant cloud networks to be built without a virtual network overlay. See [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation) for  more detail.

{% include backtotopbutton.html %}

---

#### 12. Does it have any layer 2 semantics?

No. Cloud Native applications are built on simple cloud networks where no layer 2 networks are available. Romana implements only the features that are needed for [Cloud Native Networks](/cloud/cloud_native_networks/).

{% include backtotopbutton.html %}

---

#### 13. How does it scale?

Since Romana uses a distributed, service oriented approach, each service can scale by simply adding more service instances. See [Romana Architecture](/how/romana_arch/) for details.

{% include backtotopbutton.html %}

---

#### 14. Will I run out of IPv4 Addresses?

Probably not. A Romana network that has the full use of a 10/8 network can accommodate up to 16 million endpoints. Even considering how Romana's IPAM would allocate these across CIDRs, this is a very large number of endpoints.

The latest [OpenStack User Survey (p34)]( https://www.openstack.org/assets/survey/Public-User-Survey-Report.pdf) indicates that about 36% of deployments have less than 10 Compute Nodes and about 78% of deployments have less than 100. So, for a large majority of cloud deployments there will be ample addresses. 


{% include backtotopbutton.html %}

---

#### 15. What are the plans for IPv6

Since Romana tenant and segment isolation relies on [IP addressing](/how/romana_details/#romana-tenant-isolation), larger deployments will exceed the limit of what IPv4 can support.  For this reason, Romana will support IPv6 as soon as practical. However, it is unlikely to be part of the first v1.0 Production Release 
 
{% include backtotopbutton.html %}

---

#### 16. Does Romana Support Overlapping IP addresses?

No. Romana does not support overlapping IP addresses. To maintain the structure of the [routed access datacenter design](/how/background/#routed-access-datacenter/), IP addresses need to be conform to the address hierarchy.
  
{% include backtotopbutton.html %}

---

#### 17. How does Romana handle NAT?

The current release dose not perform NAT on its own. NAT can be done as needed by a gateway router by forwarding traffic to the device. Having control over the routes also allows external IP addressed to be assigned to endpoint interfaces, avoiding NAT entirely.

Variations of this same approach will allow consolidation of external IPs in DMZ, etc. However, the current release does not support automated configuration of these kinds of individual routes. See the Romana [Roadmap](/roadmap/) for details on what is currently supported.

{% include backtotopbutton.html %}

---

#### 18. What kind of performance gains can I expect?

Since Romana does not require an overlay, no packet encapsulation is required and since traffic runs directly on the physical network, it can take more direct paths.

When encapsulation is performed on the host, it can consume about [4-6% of the CPU as well as about 3-4% of bandwidth](/how/background/#vxlan-tenant-isolation). However, just as important as reclaiming this performance, eliminating the overlay avoids the intermediate router hops that are necessary to forward traffic across networks.  With more direct network paths, Romana has reduced latency by about 85% for routed VXLAN traffic in OpenStack.

The table below summarizes the relative performance of an OpenStack network with and without Romana.

| Time | North/South | North/South | East/West | East/West |
| | Local | Remote | Local | Remote |
| Native OpenStack | 1.51 ms | 1.51 ms | 0.24 ms | 0.85 ms |
| Romana Networks | 0.24 ms | 0.77 ms | 0.24 ms | 0.77 ms |
| *Relative Performance* | Local | Remote | Local | Remote |
| Native OpenStack | 100% | 100% | 100% | 100% |
| Romana Networks | 16% | 51% | 100% | 90% |
{: class='romanatable'}


See this [Performance](/how/performance) page for more detail.

{% include backtotopbutton.html %}

---

#### 19. Is there any training available? 

Soon. We are working with a partner to develop the curriculum for a one day, hands on training workshop and have the first classes in the San Francisco Bay Area sometime in March 2016. 

If you are interested in learning more, send an email to [training@romana.io](mailto:training@romana.io).

{% include backtotopbutton.html %}

---

