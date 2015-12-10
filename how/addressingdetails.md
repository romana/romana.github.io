---
layout: page
title: Addressing Details
firstnav: 2
secondnav: 3
permalink: /how/addressing/
---

#### Topics

- [Introduction](/how/details.html/#introduction)
- [Routed Access Datacenter](/how/details.html/#routed-access-datacenter)   
- [Virtualization hosts](/how/details.html/#virtualization-hosts) 
- [VXLAN Tenant Isolation](/how/details.html/#vxlan-tenant-isolation)
- [Romana Tenant Isolation](/how/details.html/#romana-tenant-isolation)
- [IP Address Management](/how/details.html/#ip-address-managment)
- [Host Agent and Route Manager](/how/details.html/#host-agent-and-route-manager) 
- [Microservices](/how/details.html/#microservices) 
- [Service Insertion](/how/details.html/#service-insertion) 
- [Policy Based Control](/how/details.html/#policy-based-control)  

---

#### Addressing Introduction

[Yuk. To be revised]

The Romana Cloud Native SDN solution consist of several software components organized as microservices. However, for this discussion of the concepts behind Romana, we simplify the architecture to include only the Route Controller, IP Address Manager, and Host Agent. 

After the concepts behind Romana are clear, the remainder of the architecture will be easier to understand. The architecture is described in detail [here](/design/).


---

#### Routed Access Datacenter

To completely understand the operation of Romana, it is important to first understand the basics of a layer 3 routed access datacenter design. 

Today's modern datacenters are frequently built using a Spine/Leaf network design. The details of this approach are beyond the scope of this document, but several good resources are available [here]( http://www.cisco.com/c/en/us/products/collateral/switches/nexus-5000-series-switches/guide_c07-673997.html), [here]( https://www.arista.com/assets/data/pdf/DesignGuides/Arista-Universal-Cloud-Network-Design.pdf), and [here](http://searchdatacenter.techtarget.com/feature/Data-center-network-design-moves-from-tree-to-leaf).

These kind of layer 3 designs are popular because of their simplicity and performance and are often used as the underlay network for cloud deployments. An important point to remember about these designs is that they frequently take advantage of [route aggregation]( http://www.linktionary.com/r/route_aggregation.html) to further simplify operations.

Briefly stated, route aggregation is a method of simplifying router configuration by minimizing the number of routes a network device needs to maintain. This is achieved by organizing network address ranges (CIDRs) in a way that is 'topologically significant'. That is, each part of the network can only contain addresses from a specific CIDR block. By organizing the addresses hierarchically, the number of routes needed to reach every endpoint is reduced. 

The diagram below shows how route aggregation makes all endpoints in the network reachable with just a few routes configured on the leaf and spine devices. It illustrates how a 10/8 network (with up to 16M IP addresses) could be partitioned across two spines and four leaves so that each spine port forwards packets to one of four /10 networks (one on each leaf) and each leaf forwards packets to one of 64 ports configured with a /16 network.

![Route Aggregation]({{ site.baseurl }}/images/Aggregation.png)

With this configuration, each spine device is configured with the *same* four static routes, one to each leaf device. Each leaf device is configured with one route to each port (ignoring ECMP and other fabric related configuration details of spine or leafs). The routes on the leafs are sequential and differ across leaf devices only by an offset in the second octet of the CIDR.

The simplicity of this design is undeniable. It is compact, intuitive and predictable, and delivers high performance as well. Which is why it is so popular among large data center operators and the basis for most cloud network underlay designs. 

A constraint imposed by a routed access design is that it *requires endpoint IP addresses be assigned in a way that conforms to the route aggregation hierarchy*. 

One obvious way to relax this constraint is to update the network devices with individual host routes (/32 CIDRs) to the specific endpoints that do not conform to the hierarchy. 

However, updating the devices with host routes introduces the additional requirement (and complexity) of running a route distribution protocol like BGP, which requires additional skills and training. Also, at the limit, injecting host routes, by whatever means, will eventually overflow the capacity of the devices.

Romana leverages the operational simplicity of this approach and extends the use of hierarchical addressing further, down to tenants, and even their individual network segments. 

To maintain overall simplicity of the design, Romana avoids running BGP on hosts and requiring host routes on network devices by incorporating an intelligent [IP Address Management System](#ip-address-management) to ensure endpoints get addresses that conform to the hierarchy.

[(^Back to Top^)](#topics)

---

#### Virtualization Hosts

Allocating a /16 CIDR and 64K IP addresses to a single port does not make a lot of sense until you realize that the host attached to this port is actually a virtualization host that will use these available IP addresses for local VMs or containers.

For example, on the [Google Compute Engine]( https://cloud.google.com/compute/) IaaS platform, when you launch a Kubernetes VM, by default it is allocated a [complete /24 network]( http://kubernetes.io/v1.0/docs/admin/networking.html#how-to-achieve-this ) so that when a pod is launched on that VM, it gets one of the possible 255 IP addresses from within the assigned range. 

Using this as a simple baseline example, if you allocate a /24 range to each VM, having a /16 available for the entire virtualization host would accommodate up to 253 VMs (255 minus gateway and broadcast addresses, but for simplicity going forward will use 255).

The diagram below shows how Romana extends the [routed access datacenter](#routed-access-datacenter) design into the virtualization host by configuring them as a router in the overall datacenter address hierarchy. The router on the host forwards traffic to one of 253 VMs, where each VM gets one of the available /24 networks for its own use.

![Virtualization Hosts]({{ site.baseurl }}/images/vHosts.png)

Routing traffic on the host as described here is supported directly in the Linux kernel. No additional data plane forwarding software is necessary. The host needs to be configured with routes, just like a network device. The Romana [Host Agent](/design/) adds the routes on the host whenever a new endpoint is added.

*Unlike other layer 3 cloud networking designs which do not take advantage of hierarchical addressing, when a new endpoint is added to a Romana network, only the local host needs to be updated.* 

If an actual cloud datacenter were build using the assumptions from the previous example, the 16M IP addresses available in the 10/8 network would be allocated across 255 virtualization hosts, where each could support up to 255 VMs, with each VM supporting up to 255 IP addresses for local endpoints.

Alternative configurations are easily designed by simply changing how many address bit are assigned to network ports (i.e. max hosts supported). A /18 to each port (10 bits vs only 8 bits for the /16 in the previous example), would allow 2 more address bits available for ports, supporting four times as many hosts (up to 1K, 2^8 vs 2^10), but each host could accommodate only one fourth the number of endpoints (2^16 v. 2^18).  

Other variations include using only a portion of the complete 10/8 for smaller configurations, such as a 10/10 for 4M endpoints, a 10/11 for 2M endpoints, etc. Smaller configurations are possible as well where just a few hosts are attached directly to a flat network.

The important point here is that Romana configures the virtualization host to act just like any other router in the datacenter, forwarding traffic to local tenant endpoints. This is an obvious and natural extension of the highly successful layer 3 routed access datacenter design on to virtualization hosts. 

> Romana allows you to configure the number of bits in the address that are used for each purpose so that you can design your environment in whatever way best suits your needs. IPv6 is also [supported](github issue like) so that larger data center designs are possible as well.

However, for multi-tenant cloud orchestration systems to take full advantage of this design, the problem of tenant isolation still needs a solution.

[(^Back to Top^)](#topics)

---

#### VXLAN Tenant Isolation

The traditional method to maintain tenant isolation is to create individual layer 2 segments on top of a layer 3 underlay network. These overlay networks use VXLAN encapsulation (or similar encapsulation protocols) which supports up to 16M isolated network segments. And just like other layer 2 segments, they must be configured with a CIDR address range and gateways to routers that forward traffic to other VLANs and networks, as necessary. 

Assigning a VXLAN to individual tenant network segments provides layer 2 VLAN style isolation for every segment. If a NAT service is also available, segments can also support overlapping IP addresses. 

While VXLAN technology is reasonably mature and stable, bandwidth and CPU performance overhead are often cited as [areas for concern]( http://www.enterprisenetworkingplanet.com/netsp/vxlan-beyond-the-hype.html).

This is because the overlay requires a Virtual Tunnel Endpoint (VTEP) processor to add a header and to insert the correct field value for each packet that enters or exits the VXLAN. While line rate throughput can be achieved, the actual observed performance impact is [difficult to characterize](http://blog.ipspace.net/2015/02/performance-of-hypervisor-based-overlay.html). However, with header overhead [reducing bandwidth by ~6%](http://packetpushers.net/vxlan-udp-ip-ethernet-bandwidth-overheads/), and [CPU overhead taking at least 3%](http://chinog.org/wp-content/uploads/2015/05/Optimizing-Your-Virtual-Switch-for-VXLAN.pdf) the overall performance impact cannot be ignored.   

But more important to consider than simple stand alone performance benchmarks is the cumulative latency introduced by encap/decap cycles along the complete path. At a minimum, all east/west traffic requires an encap at the source and a decap at the destination, or one complete cycle. However, for all routed VXLAN and north/south traffic, there is an *additional* encap/decap cycle for *each* intermediate router and/or service function. Additionally, since overlay networks remove all topology context from the network, inefficient packet paths are unavoidable.

For example, the diagram below shows the path packets take when OpenStack VMs on different tenant VXLAN segments communicate. There is an extra encap/dcap cycle required for the router on another host to forward the traffic to the proper endpoint. 

With Romana this extra router hop is avoided and the top-of-rack round trip as well as the three encap cycles are eliminated. As a result latency is greatly reduced (in some cases by 85%).

![OpenStack VXLAN Routing]({{ site.baseurl }}/images/vxlan.png) 

If performance were all it cost to run VXLAN, considering the benefits of segment isolation, it could still be a bargain. However, from an operational perspective, there are other critical challenges to running VXLANs that are not easily addressed.

1. *Visibility*: VXLAN Encapsulation hides traffic inside of IP packets that can not be seen with standard network monitoring tools. Actual paths through the physical network are difficult to diagnose because endpoint addresses are inside VXLANs and could be located anywhere. Since encapsulated traffic lacks the topology context of the physical network, trouble-shooting problems is especially challenging. 

2. *Manageability*: VXLANs use a VXLAN Network Identifier (VNID) identifying the segment to which the traffic belongs. Managing these VNID is conceptually similar to managing VLAN IDs, except instead of managing 4K IDs, you need a system that can handle up to 16M. The VNIDs need to be propagated to all VTEPs, which itself is a large management problem that requires some measure of VTEP compatibility and/or interoperability.

3. *Interoperability*: When running VXLAN overlays, operators will invariably require VTEPs on virtualization hosts, network devices and other VXLAN gateways. Each VTEP potentially could have different APIs, versions or come from different vendors.  There are no interoperability standards (or even best practices) for VTEPs, which makes coordinating VNIDs, even with a commercial Enterprise SDN solution, especially challenging.

4. *Cost*: To avoid the latency impact of encapsulation, new network devices with VTEP hardware acceleration may be required. In addition, an SDN solution of some kind is necessary to coordinate VNIDs. There is also the operational burden of the integrating and managing the entire environment on an ongoing basis. These costs are not insignificant.

Container orchestration systems such as Docker or Kubernetes also use VXLANs for segment isolation. Today, when these systems are run on OpenStack VMs, the result is *double* encapsulation! It will be a long while before these conflicting deployment models are reconciled.

The net result of higher costs, architectural incompatibility, operational challenges and performance overhead of running VXLANs have operators looking for better ways to provide secure tenant isolation.

[(^Back to Top^)](#topics)

---

#### Romana Tenant Isolation 

What operators need is a solution that supports a multi-tenant network isolation model that avoids the cost and complexity of overlay networks, while maintaining the simplicity of the routed access datacenter design.

Romana Cloud Native SDN delivers this exact solution. 

The key insight that lets Romana achieve this operational goal is recognizing that [Cloud Native](/cloud-native-sdn/) applications do not require layer 2 networks at all. For example, the [Kubernetes network model]( http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model) indicates only that:

> Kubernetes imposes the following fundamental requirements on any 
 networking implementation (barring any intentional network segmentation 
 policies):

>>* all containers can communicate with all other containers without NAT
>>* all nodes can communicate with all containers (and vice-versa)
without NAT
>>* the IP that a container sees itself as is the same IP that others see it as

Since Cloud Native applications do not require layer 2 networks, Romana tenant isolation can avoid an overlay entirely, provided that isolation can be maintained at layer 3.

Fortunately, this can be achieved with a layer 3 firewall by configuring IP tables rules on the hypervisor that allow only the endpoints on the same segment to communicate. The local Romana Agent is available to configure these firewall rules as needed. 

However, maintaining these kinds of firewall rules and access control list (ACLs) for all endpoints is a large and complex data management problem. The problem becomes even more difficult when more advanced traffic management policies are required [Is this true? How complex is this, really?].

To reduce this complexity and simplify the solution further, Romana extends the idea of capturing network topology in the IP address to also include a segment identifier in the address as well.

Similar to the way the high order address bits identify a specific port in the network and assigning it its own /16 network, Romana can assign another set of bits to identify individual segments. Each segment, therefore, has its own unique CIDR on every host.  

The power of this addressing scheme is that this segment CIDR now has *application level context*, which Romana uses to simplify the firewall rules. Also, in addition to the CIDR being the handle by which all traffic in and out of the segment can be controlled, capturing this application level context in the CIDR makes *every layer 3 control device available for application level policy enforcement*.

Specifically, Romana assigns a set of bits to be a segment identifier and further splits it in to a Tenant ID and a Segment ID. This creates Tenant CIDRs as well as Tenant Segment CIDRs within the tenant range, each with their own application level context.

The diagram below illustrates how an IP address can be partitioned to capture host, tenant and segment identity. Note, for simplicity, the example uses 8 bits for each range, which makes the resulting IP addresses readable in four octet dot address notation. 

In this example all traffic for Host 1 (Host ID = 1), would go to the 10.1/16 network. Of this, traffic to Tenant 1 (ID=1) would be directed to the 10.1.1/24 network. Traffic for Tenant 2 (ID=2) would go to 10.1.2/24. Traffic to Tenant 1 Segment 1 (ID=1) would go to 10.1.1.16/28 (1 in bits 25-28 of address, or 00010000=16). For Host 2, all CIDRs would be the same, except that 1 would be replaced by 2, the ID of Host 2.

![Route Aggregation]({{ site.baseurl }}/images/cidr.png)

Endpoints that are in a specific segment would have IP addresses from the CIDR range of the segment. VM1 (ID=11) on Host 1 for Tenant 1 Segment 1 would get 10.1.1.27 (16+11=27).

Romana assigning addresses to application endpoints within the Tenant and Segment CIDRs enhances them with application level context. This context is used directly on the host to provide Tenant and Segment isolation. 

Segment isolation is just one example of how Romana uses application level context to simplify multi-tenant cloud networking in the same way that 'topology significant' addressing simplified data center design and operations.

In summary, for each virtualization host, there is a CIDR that identifies traffic to the entire host; a longer CIDR that identifies traffic to each tenant on the host; and for each tenant, there is an even longer CIDR that identifies traffic to each segment. Each CIDR captures important application level context. 
 
|Level |  | ID  |  |CIDR  |
|---|---|---:|---|---| ---|
|Host  |  | 1  |  | 10.1/16  |
|Tenant  |  | 1  |  | 10.1.1/24    |
|Segment  |  | 1  |  | 10.1.5.16/28    |



Across all hosts, the total number of CIDRs is the total number of routes on each Host, times the number of hosts deployed. It is the job of the Romana [Route Manager](#route-manager) to create, set, update and manage these routes across the entire network. [details TBD]

 [(^Back to Top^)](#topics)

---

#### IP Address Management

Attaching [application level context](#romana-tenant-isolation) to endpoint addresses is a powerful tool to simplify cloud networking. The remaining problem is ensuring that IP addresses get assigned in a way that conforms to this design.

Romana does this with its own IP Address Management system what works with cloud orchestration systems such as OpenStack and Kubernetes and maintains a list of all Tenants and the network Segments and endpoints they create. 

For OpenStack, Romana provides a standard OpenStack ML2 plugin. Whenever a new Tenant, Segment or endpoint is created, Romana updates its database. 

When a new VM is launched in OpenStack, Romana learns which Tenant is launching it and the segment on which they wish it to be placed. When OpenStack identifies the host for the VM, the Romana IPAM calculates the IP address based on the Host ID, Tenant ID and Segment ID.  This address is then assigned to the VM interface using DHCP. mechanism.

For other cloud orchestration systems, such as Kubernetes, a similar process takes place to make sure all application endpoints are assigned the proper addressees.

[(^Back to Top^)](#topics)

---

#### Host Agent and Route Manager

Romana uses the native routing functions in the Linux kernel for packet forwarding and does not require any additional kernel software (dataplane kernel modules, etc.). Configuring routes on the Host is done with standard linux ip route commands, which are issued locally via the Romana Host Agent. 

Before any endpoints can be configured, the Route Host Agent must first configure the default gateway interface on the host that all local endpoints use. When a new endpoint is launched on a host, the Host Agent to configure a route. The routes are simply the default route to the host gateway. 

However, each route can be reconfigured with a different default gateway to direct traffic to an alternative device for [Service Insertion](#service-insertion) or to deal with [VM migration](github issue). Such special routes are maintained by the Romana Route Manager, which communicates with the Romana Host Agent on one or more hosts, as needed.


[(^Back to Top^)](#topics)

---

#### Microservices

A powerful advantage of the Romana layer 3 traffic control system is that it is inherently service oriented. Recognizing that network Segments define a Service boundary, the CIDR that controls traffic in and out can be used to steer traffic between Microservices.

In addition, the Romana CIDR also has service context that can be used to control how policies are to be applied to the traffic that reaches a service. Tenant and Segment isolation is just one example of how Romana can apply policy to network traffic throughout the system.

The default gateway of the endpoints in a Segment (i.e. within a Microservice) can be assigned to a Load Balancer, or any other Service Function device to act as the service endpoint. These front end Service Functions can apply any kind of user policy that is required. 

This kind of front end Service Function is just one example of the more general Service Insertion capability of Romana.

[(^Back to Top^)](#topics)

---

#### Service Insertion

Connecting different Microservices to compose more complicated applications is typically done directly by the application developer by naming service endpoints and adding them to a Service Discovery registry. When services need to communicate, they look for the named service in the registry to learn the IP address (or addresses) where the service is available, and open a connection directly with the service endpoint. The path that traffic actually takes between service endpoints is generally not something developer cares about. 

Operators, on the other hand, frequently want to steer traffic through different paths because of different policy objectives (Security, Encryption, etc.).  These traffic policies can not be left to the developer to apply, which means operators need a way to transparently steer traffic through Service Functions, independent of how individual microservices ordinarily communicate.

Since Romana has individual CIDRs that identify traffic flows, it can insert Service Functions along the path simply by specifying the next hop router to be the IP address of the next Service Function in the chain. The Route Manager already does when it sets up a route. Modifying the route in this way is straightforward. All that is needed is a way to define the path.

Defining these traffic path is the job of the [Romana Service Policy Manager](#policy-based-control).

A more detailed overview of Service Insertion is available [here](/how/chain/)

 [(^ Back to Top^)](#topics)

---

#### Policy Based Control

[(^Back to Top^)](#topics)



