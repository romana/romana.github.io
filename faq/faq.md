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
3. [Does Romana work with OpenStack](#does-romana-work-with-openstack)?
4. [Does Romana work with Docker](#does-romana-work-with-docker)?
5. [Does Romana work with Kubernetes](#does-romana-work-with-kubernetes)?
6. [Will Romana run in AWS](#will-romana-run-in-aws)?
7. [How are network segments isolated](#how-are-network-segments-isolated)?
8. [Does it have any layer 2 semantics](#does-it-have-any-layer-2-semantics)?
9. [How does it scale](#how-does-it-scale)?
10. [What are the plans for IPv6](#what-are-the-plans-for-ipv6)?
11. [Does Romana support overlapping IP addresses](#does-romana-support-overlapping-ip-addresses)?
12. [How does Romana handle NAT](#how-does-romana-handle-nat)?

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

#### 3. Does Romana work with OpenStack?

Yes. Romana works with cloud orchestration systems like OpenStack to launch VM and container endpoints. Romana integrates with OpenStack through a Modular Layer 2 device driver and an interface to its new IPAM API.

See current [Roadmap](/roadmap/) for details.


{% include backtotopbutton.html %}

---

#### 4. Does Romana work with Docker?

It will. The current [v0.1 release](/roadmap/) does not support Docker, but it is planned for release v0.2.

{% include backtotopbutton.html %}

---

#### 5. Does Romana work with Kubernetes?

It will. The current [v0.1 release](/roadmap/) does not support Kubernetes, but it is planned for release v0.2.

{% include backtotopbutton.html %}
---

#### 6. Will Romana run in AWS?

Yes. The current release supports OpenStack running in AWS EC2. See current [Roadmap](/roadmap/) for details.

{% include backtotopbutton.html %}

---

#### 7. How are network segments isolated?

Romana uses a new layer 3 based approach for tenant and segment isolation. Romana encodes tenant and segment identifiers directly in the IP address and ip tables firewall rules are set to provide isolation. This enables multi-tenant cloud networks to be built without a virtual network overlay. See [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation) for  more detail.

{% include backtotopbutton.html %}

---

#### 8. Does it have any layer 2 semantics?

No. Cloud Native applications are built on simple cloud networks where no layer 2 networks are available. Romana implements only the features that are needed for [Cloud Native Networks](/cloud/cloud_native_networks/).

{% include backtotopbutton.html %}

---

#### 9. How does it scale?

Since Romana uses a distributed, service oriented approach, each service can scale by simply adding more service instances. See [Romana Architecture](/how/romana_arch/) for details.

{% include backtotopbutton.html %}

---

#### 10. What are the plans for IPv6

Since Romana tenant and segment isolation relies on [IP addressing](/how/romana_details/#romana-tenant-isolation), larger deployments will exceed the limit of what IPv4 can support.  For this reason, Romana will support IPv6 as soon as practical. However, it is unlikely to be part of the first v1.0 Production Release 
 
{% include backtotopbutton.html %}

#### 11. Does Romana Support Overlapping IP addresses?

No. Romana does not support overlapping IP addresses. To maintain the structure of the [routed access datacenter design](/how/background/#routed-access-datacenter/), IP addresses need to be conform to the address hierarchy.
  
{% include backtotopbutton.html %}


#### 12. How does Romana handle NAT?

The current release dose not perform NAT directly. NAT can be done as needed by a gateway router by forwarding traffic to the device directly. Having control over the routes also allows external IP addressed to be assigned directly to endpoint interfaces, avoiding NAT entirely.

Variations of this same approach will allow consolidation of external IPs in DMZ, etc.

The current release does not support automated configuration of these kinds of individual routes. See the Romana [Roadmap](/roadmap/) for details on what is currently supported.


