---
layout: page
title: Helpful Network Background Information
menu_text: Background
nav_text: NA
firstnav: 2
secondnav: 4
permalink: /how/background/
---

To better understand the operation of Romana and how it is different from other cloud networking alternatives, it is helpful to know the basics of layer 3 routed access datacenter networks, multi-tenant network isolation and service insertion. A brief description of each is provided below.

### Topics

- [Routed Access Datacenter](/how/background/#routed-access-datacenter)   
- [VXLAN Tenant Isolation](/how/background/#vxlan-tenant-isolation)
- [Service Insertion](/how/background/#service-insertion) 

---

### Routed Access Datacenter

Today's modern datacenters are frequently built using a spine/leaf network design. The details of this approach are beyond the scope of this document, but several good resources are available [here]( http://www.cisco.com/c/en/us/products/collateral/switches/nexus-5000-series-switches/guide_c07-673997.html), [here]( https://www.arista.com/assets/data/pdf/DesignGuides/Arista-Universal-Cloud-Network-Design.pdf), and [here](http://searchdatacenter.techtarget.com/feature/Data-center-network-design-moves-from-tree-to-leaf).

These designs are popular because of their simplicity and performance and are often used as the underlay network for cloud deployments. Unlike layer 2 networks [running STP](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol), they can take advantage layer 3 protocols such as [ECMP](https://en.wikipedia.org/wiki/Equal-cost_multi-path_routing) to take advantage of all available physical links between network devices. In addition, they frequently take advantage of [route aggregation]( http://www.linktionary.com/r/route_aggregation.html) to further simplify operations.

Briefly stated, route aggregation is a method used to simplify router configuration by minimizing the number of routes a network device needs to maintain. This is achieved by assigning network addresses in a way that is 'topologically significant'. That is, each router gets assigned a portion of the entire IP address range based on where the router is located in the network topology. By organizing the addresses hierarchically, the number of routes needed to reach every endpoint is reduced. 

The diagram below shows how route aggregation makes all endpoints in the network reachable with just a few routes configured on the leaf and spine devices. It illustrates how a 10/8 network (with up to 16M IP addresses) could be partitioned across two spines and four leaves so that each spine port forwards packets to one of four /10 networks (one on each leaf) and each leaf forwards packets to one of 64 ports configured with a /16 network.

![Route Aggregation]({{ site.baseurl }}/images/routeagg.png)

With this configuration, each spine device is configured with the *same* four static routes, one to each leaf device. Each leaf device is configured with *one route to each port* (ignoring ECMP and other fabric related configuration details of spines or leafs). The routes on the leafs are sequential and differ across leaf devices only by an offset in the second octet of the CIDR.

The simplicity of this design is undeniable. It is compact, intuitive and predictable, and delivers high performance as well. Which is why it is so popular among large data center operators and the basis for most cloud network underlay designs. 

#### Virtualization Hosts

Allocating a /16 CIDR with 64K IP addresses to a single port does not make a lot of sense until you realize that for cloud computing networks, the host attached to this port is a virtualization host that will use these available IP addresses for local VMs or containers.

For example, on the [Google Compute Engine]( https://cloud.google.com/compute/) IaaS platform, when you launch a Kubernetes VM, by default it is allocated a [complete /24 network]( http://kubernetes.io/v1.0/docs/admin/networking.html#how-to-achieve-this ) so that when pods are launched on that VM, they will all be on the same network since they each get one of the addresses from within the assigned range. 

Using this as a simple baseline example, if you allocate a /24 network to each VM, having a /16 available for the entire virtualization host would accommodate up to 253 VMs (255 minus gateway and broadcast addresses). 

The diagram below shows the routed access datacenter design extended onto the virtualization hosts by configuring them as routers in the overall datacenter address hierarchy. The routers on the host forward traffic to one of 253 VMs, where each VM gets one of the available /24 networks for its own use.

![Virtualization Hosts]({{ site.baseurl }}/images/vHosts.png)

It is important to realize that a constraint imposed by this kind of routed access design is that it *requires endpoint IP addresses be assigned in a way that conforms to the route aggregation hierarchy*. 

> Note: One obvious way to relax this constraint is to update the routers with individual host routes (/32 CIDRs) to the specific endpoints that do not conform to the hierarchy. 
<br><br>
However, updating routers with individual host routes for each endpoint introduces the additional requirement (and complexity) of running a route distribution protocol like BGP, which requires additional skills and training. Also, at the limit, injecting host routes, by whatever means, will eventually overflow the capacity of the devices.
<br><br>
Also, since virtualization hosts are configured as routers in the design hierarchy, in addition to requiring that all network devices run BGP (perhaps ~6-24 devices), every host needs to run it as well (perhaps hundreds of devices). This increases operational and administrative complexity substantially.

If an actual cloud datacenter were built using the assumptions from the example above, the 16M IP addresses available in the 10/8 network would be allocated across 255 virtualization hosts (64 attached to each of the 4 leaf devices), where each host could support up to 255 VMs, with each VM supporting up to 255 IP addresses for local endpoints.

Alternative configurations are easily designed by simply changing how many address bits are dedicated to identify hosts (i.e. routers) attached to leaf switch ports. In the previous example, 8 bits were used to identify hosts (i.e. 8 Host ID bits), resulting in up to 2^8 or 64 hosts. Eight Host ID bits added to the eight bit netmask from the 10/8 CIDR gives each connected host its own /16 network. Using 10 Host ID bits instead of eight allows four times as many connected hosts, but each would only accommodate one fourth the number of endpoints.

Other variations include using only a portion of the complete 10/8 for smaller configurations, such as a 10/10 for 4M endpoints, a 10/11 for 2M endpoints, etc. 

Extending the routed access design on to the hosts also allows the design to work when all hosts are on a flat layer 2 network. In this case, there would need to be a route configured on each host to the router on every other host to maintain the fully routed design.

> [Romana v0.6 Release](/try_romana/) builds an OpenStack DevStack cluster in AWS where each OpenStack Node runs as an EC2 instance in a VPC. Since there are no spine or leaf devices, routes are configured on every Node to every other Node to implement the fully routed design.

The important point here is that the simplicity of the routed access design is extended on to the virtualization host where it acts just like any other router in the datacenter, forwarding traffic to local tenant endpoints. This is an obvious and natural extension of the highly successful layer 3 routed access datacenter.

One significant challenge for using this design to simplify multi-tenant cloud networks is to ensure that VM and container endpoints get IP addresses that conform to the address hierarchy.

The other is to maintain tenant isolation.

{% include backtotopbutton.html %}

---

### VXLAN Tenant Isolation

The traditional method to maintain tenant isolation is to create individual layer 2 segments on top of a layer 3 underlay network. These overlay networks typically use VXLAN encapsulation (or similar encapsulation protocol) which supports up to 16M isolated network segments. And just like other layer 2 segments, they must be configured with a CIDR address range and gateways to routers that forward traffic to other VLANs and networks, as necessary. 

Assigning a VXLAN to individual tenant network segments provides layer 2 VLAN style isolation for every segment. If a NAT service is also available, segments can also support overlapping IP addresses. 

While VXLAN technology is reasonably mature and stable, bandwidth and CPU performance overhead are often cited as [areas for concern]( http://www.enterprisenetworkingplanet.com/netsp/vxlan-beyond-the-hype.html).

This is because the overlay requires a Virtual Tunnel Endpoint (VTEP) processor to add a header and to insert the correct field value for each packet that enters or exits the VXLAN. While line rate throughput can be achieved, the actual observed performance impact is [difficult to characterize](http://blog.ipspace.net/2015/02/performance-of-hypervisor-based-overlay.html). However, with header overhead [reducing bandwidth by ~6%](http://packetpushers.net/vxlan-udp-ip-ethernet-bandwidth-overheads/), and [CPU overhead taking at least 3%](http://chinog.org/wp-content/uploads/2015/05/Optimizing-Your-Virtual-Switch-for-VXLAN.pdf) the overall performance impact cannot be ignored. The reduced MTU size, for example may introduce packet fragmentation that could significantly impact [performance](http://www.networkworld.com/article/2224654/cisco-subnet/mtu-size-issues.html).  

But more important to consider than simple stand alone performance benchmarks is the cumulative latency introduced by encap/decap cycles along the complete path. At a minimum, all east/west traffic requires an encap at the source and a decap at the destination, or one complete cycle. However, for all routed VXLAN and north/south traffic, there is an *additional* encap/decap cycle for *each* intermediate router and/or Service Function. This is especially problematic for Cloud Native (i.e. microservice oriented) applications since they frequently apply Service Functions (i.e. load balancers) between *each* microservice.

In addition, since overlay networks remove all topology context from the network, inefficient packet paths are unavoidable.

For example, the diagram below shows the path packets take when OpenStack VMs on different tenant VXLAN segments communicate. There is an extra encap/dcap cycle required for the router on the Neutron host to forward traffic to the proper endpoint. 

![OpenStack VXLAN Routing]({{ site.baseurl }}/images/vxlan.png) 

If performance were all it cost to run VXLAN, considering the benefits of segment isolation, it could still be a bargain. However, from an operational perspective, there are other critical challenges to running VXLANs that are not easily addressed.

1. *Visibility*: VXLAN Encapsulation hides traffic inside of IP packets that can not be seen with standard network monitoring tools. Actual paths through the physical network are difficult to diagnose because endpoint addresses are inside VXLANs and might be located anywhere on the network. Since encapsulated traffic lacks the topology context of the physical network, trouble-shooting problems is especially challenging. 

2. *Manageability*: VXLANs use a VXLAN Network Identifier (VNID) that identifies the segment to which the traffic belongs. Managing these VNID is conceptually similar to managing VLAN IDs, except instead of managing up to 4K IDs, you need a system that can handle up to 16M. The VNIDs need to be propagated to all VTEPs, which itself is a large management problem that requires some measure of VTEP compatibility and/or interoperability.

3. *Interoperability*: When running VXLAN overlays, operators will invariably require VTEPs on virtualization hosts, network devices and other VXLAN gateways. Each VTEP potentially could have different APIs, versions or come from different vendors.  There are no interoperability standards (or even best practices) for VTEPs, which makes coordinating VNIDs difficult, even with a commercial Enterprise SDN solution.

4. *Cost*: To avoid the latency impact of encapsulation, new network devices with VTEP hardware acceleration may be required. In addition, an SDN solution of some kind is necessary to coordinate VNIDs. There is also the operational burden of the integrating and managing the entire environment on an ongoing basis. These costs are not insignificant.

Container orchestration systems such as Docker or Kubernetes also use VXLANs for segment isolation. Today, when these systems are run on OpenStack VMs, the result is *double* encapsulation! It will be a long while before these conflicting deployment models are reconciled.

The net result of higher costs, architectural incompatibility, operational challenges and performance overhead of running VXLANs have operators looking for better ways to provide secure tenant isolation.

{% include backtotopbutton.html %}

---

### Service Insertion

Service Insertion and Service Chaining are terms used to describe the ability to modify the *path* traffic will use to traverse the network on its way to its final destination. The modified path typically includes one or more network Service Function devices (SFs) that may filter and/or modify the traffic at the network layer, according to an operator-defined policy. An example of an SF is a load balancer, firewall or IDS.

Service Insertion is related to, but different from, application-level request routing. Application request routing is the process by which requests are forwarded to one or more application service endpoints that are available for further processing. Forwarding decisions for application request routing may be based on any application oriented criteria such as the kind of request being made, or even the identity of the requester. Application request routing typically specifies *only* the destination of requests and *not* the path requests should use to get there.

Service Insertion is also different from service composition, which is the collection of fixed, developer-specified paths that requests must traverse to complete an application function. An example of this kind of logical path would be specifying what is necessary to complete a user request. For example, a user request requires a path to a webserver, from a web server to an application server, and from an application server to a database server, and so on.

Sometimes these functional destinations are specified directly by their numerical IP address, but today with microservice based application architectures, they are more likely to be specified by name. In this case, a separate name-to-IP resolution service, such as DNS, would be used for service discovery. 

When a destination endpoint is specified and traffic traverses the network, the standard path will be through the default route from the source to the destination. Changing this path for Service Insertion requires reconfiguration of the devices that lie along the standard path.

What distinguishes Service Chaining from either request routing or service composition is, unlike these *developer* specified paths, the *network operator* needs to insert SFs *transparently* in the path, *independent* of how applications are designed. 

The obvious example is the operators desire to insert security devices, as needed, anywhere within the network without requiring modifications to the applications. 

The diagram below shows a Service Function (F1) inserted into the path from the source (S1) to the destination (D1). On the left, the traffic flows through the gateway directly to the destination. On the right, by modifying the gateway address of S1 to be the IP address of the Service Function, traffic will not go to the gateway. Instead, it will go to the Service Function where it will apply the desired function then forward traffic to the default gateway where it will be routed to the destination in the standard way.

![Virtualization Hosts]({{ site.baseurl }}/images/insertion.png)

This is a simple example where there is only one SF to be inserted and it is on the same local network as the source.  More complex examples with multiple SFs spread across different networks would follow the same basic approach of modifying the next hop gateway address to be the IP address of the next SF in the chain.

However, when an SF is on a different network from the source (or previous SF), packets are sometimes tunneled using modified headers that also include service chain metadata. See the [Network Service Header](https://datatracker.ietf.org/doc/draft-ietf-sfc-nsh/) (NSH) and [Service Function Chaining]( https://datatracker.ietf.org/doc/rfc7498/) (SFC) RFCs for details.

Specifying Service Chains and determining what traffic uses which chain requires some kind of network traffic policy and configuration manager.

{% include backtotopbutton.html %}

---


