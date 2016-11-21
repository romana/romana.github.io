---
layout: page
title: Romana Details
menu_text: More Details
nav_text: NA
firstnav: 1
secondnav: 2
permalink: /how/romana_details/
---

To better understand the operation of Romana, it is helpful to also understand the basics of a layer 3 routed access datacenter design, tenant isolation and service chaining and insertion. If you are unfamiliar with any of these concepts, a brief summary is available [here](/how/background/).

### Topics

- [Introduction](/how/romana_details/#introduction)
- [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation)
- [IP Address Management](/how/romana_details/#ip-address-management)
- [Route Manager and Host Agent](/how/romana_details/#route-manager-and-host-agent) 
- [Microservices](/how/romana_details/#microservices) 
- [Policy Based Control](/how/romana_details/#policy-based-control)
- [Deploying Romana](/how/romana_details/#deploying-romana) 

---

### Introduction

Cloud Native Networks are built and managed by Romana's configuration and control services. Central to this approach is intelligent [IP Address Management](/how/romana_details/#ip-address-management) that extends a layer 3 [routed access design](/how/background/#routed-access-datacenter) on to hypervisors, VMs and container endpoints and the automated configuration of routes and access control. These functions are two of the Romana control services that together let operators build multi-tenant Cloud Native Networks. 

---

### Romana Tenant Isolation

A common way to maintain tenant isolation is to use VXLANs to overlay individual layer 2 segments on top of a layer 3 underlay network. A summary of this overlay approach is described [here](/how/background/#vxlan-tenant-isolation/).

However, since Cloud Native applications do not require layer 2 networks, Romana can avoid an overlay network for tenant isolation as long as isolation can be enforced at layer 3.

Fortunately, isolation can be achieved at layer 3 with a host based firewall by configuring Linux *iptables* rules on the hypervisor to allow communication only among valid endpoints. The local Romana Agent is available to configure these firewall rules as needed. 

Maintaining these firewall rules and access control list (ACLs) for all endpoints quickly grows into a large and complex data management problem. The problem becomes even more difficult when sophisticated traffic management policies are required.

To reduce rule management complexity and simplify the network further, Romana extends the way layer 3 network designs capture network topology in the IP address by also including a tenant and segment identifier. 

In the same way that a layer 3 network design assigns a network CIDR to every physical switch port, Romana assigns (from within the switch port's address range), a CIDR for each tenant.  Similarly, within each tenant network, each segment gets its own network as well.

The diagram below is an example of how Romana can partition an IPv4 address to capture host, tenant and segment identity. 

![Route Aggregation]({{ site.baseurl }}/images/cidr.png)

> Note: For simplicity, the example shown uses 8 bits for each range, which makes the resulting IP addresses readable in four octet dot address notation. The actual number of bits used for Host, Tenant and Segment IDs is configurable. 

In this example all traffic for Host 1 (Host ID = 1), would go to the 10.1/16 network. Of this, traffic to Tenant 1 (ID=1) would be directed to the 10.1.1/24 network. Traffic for Tenant 2 (ID=2) would go to 10.1.2/24. Traffic to Tenant 1 Segment 1 (ID=1) would go to 10.1.1.16/28 (1 in bits 25-28 of address, or 00010000=16). For Host 2, all CIDRs would be the same, except that 1 would be replaced by 2, the ID of Host 2.

Endpoints that are in a specific segment would have IP addresses from the CIDR range of the segment. VM1 (ID=11) on Host 1 for Tenant 1 Segment 1 would get 10.1.1.27 (16+11=27).

In summary, for each virtualization host, there is a CIDR that identifies traffic to the entire host; a longer CIDR that identifies traffic to each tenant on the host; and for each tenant, there is an even longer CIDR that identifies traffic to each segment. 

|Level|ID|CIDR|
|Host|1|10.1/16 |
|Tenant|1|10.1.1/24|
|Segment|1|10.1.1.16/28|
{: class='romanatable'}

Across all hosts, the total number of CIDRs is the total number of routes on each Host, times the number of hosts deployed. It is the job of the Romana [Route Manager](#route-manager-and-host-agent) to create, set, update and manage these routes across the entire network.

{% include backtotopbutton.html %}
---

### IP Address Management

Romana's layer 3 isolation approach requires that VM and container endpoints receive IP addresses that maintain the network address hierarchy.

Romana does this with its own IP Address Management system what works with cloud orchestration systems such as OpenStack to maintain a list of all tenants and the network segments and endpoints they create. 

For OpenStack, Romana provides an OpenStack ML2 plugin and IPAM API Driver. Whenever a new tenant, segment or endpoint is created, Romana updates its database. 

When a new VM is launched in OpenStack, Romana learns which tenant is launching it and the segment on which they wish it to be placed. When OpenStack identifies the host for the VM, Romana's IPAM calculates the IP address based on the Host ID, Tenant ID and Segment ID.  This address is then assigned to the VM interface using the OpenStack IPAM API.

For Kubernetes, IPAM integration takes place via the CNI plugin.

{% include backtotopbutton.html %}
---

### Route Manager and Host Agent

Romana uses the native routing functions in the Linux kernel for packet forwarding and does not require any additional kernel software (dataplane kernel modules, etc.). Configuring routes on the host is done with standard Linux *ip route* commands, which are issued locally via the Romana Agent. 

Before any endpoints can be configured, the Route Manager, via the Agent, must first configure the default gateway interface on the host that local endpoints use. When a new endpoint is launched on a host, the Route Manager instructs the Agent to configure a route. Normally, these routes are simply default routes to the host gateway. 

However, when a service is inserted into the path of an endpoint, this default route can be reconfigured to use a different address to forward traffic to a Service Function for [Service Insertion](#service-insertion). 

{% include backtotopbutton.html %}
---

### Microservices

Cloud Native applications are frequently deployed using a [microservices](http://thenewstack.io/best-practices-for-developing-cloud-native-applications-and-microservice-architectures/) based design pattern. This requires replicated elements within a service be identified individually, yet all be managed collectively as a unified service. These requirements are well suited to a layer 2 networking model since it provides network isolation as well as explicit access control and membership affinity through its gateway and CIDR.

Other cloud networking solutions that use layer 3 for tenant isolation are unable to take advantage of these layer 2 properties. However, since Romana maintains a CIDR for each tenant segment, it is inherently service oriented because network segments define microservice boundaries. And since network segments define a service boundary, the CIDR that controls traffic in and out of a segment can also be used to steer traffic among microservices and through operator defined [Service Functions](#service-insertion).

With Romana, the default gateway of the endpoints in a segment (i.e. a microservice) can be assigned to a Load Balancer, or any other Service Function device to act as the service endpoint. These front end Service Functions can apply any kind of user policy that is required. 

Also, since an overlay is not necessary for Romana segments, all existing (physical or virutal) layer 3 devices can become Service Functions and/or microservice endpoints. This kind of front end Service Function is just one example of the more general Service Insertion capability of Romana.

{% include backtotopbutton.html %}
---

### Policy Based Control

Romana allows the fine grained control and management of network traffic via network policies. The Romana network policies format was inspired by the Kubernetes network policy specification. However, Romana policies can be applied in Kubernetes as well as OpenStack environments. Furthermore, Romana extends the policies with additional features, such as the ability to control network traffic not only for containers or VMs, but also for bare metal servers.

See the wiki [page](https://github.com/romana/romana/wiki/Romana-policies) and [examples](https://github.com/romana/core/tree/master/policy/examples) in the Romana romana and core repositories.

{% include backtotopbutton.html %}
---

### Deploying Romana

Romana may be deployed as part of an OpenStack private cloud, or on public cloud infrastructure such as Amazon's EC2. 

Docker and Kubernetes may then run in either of these cloud environments so that application developers using these systems see no changes to their standard development workflow. 

Details on running the current release on EC2 are available for both [OpenStack](/try_romana/installation/) and [Kubernetes](/try_romana/kubernetes/).

{% include backtotopbutton.html %}





