---
layout: post
title: How it Works
date: 2015-11-3
categories:
 - how
---

## How it Works

Romana multi-tenant cloud networking is based on a new layer 3 tenancy model that encodes tenant and segment identifiers directly in to the IP address. By combining intelligent IP address management with route control and layer 3 firewalls on the hypervisor, network isolation is enforced between segments. 

Romana's intelligent IP Address Management (IPAM) system assigns endpoint IP addresses based on the segment that it is part of, as well as the physical host where it is running. This, when deployed in a modern layer 3 routed access datacenter design, allows routes to be aggregated and statically configured on physical network devices. With static routes on the physical network, only the hypervisor needs an updated route when a new endpoint is created. 

Also, since routes are static, no route distribution protocol is necessary, further simplifying network design.

Service insertion is implemented by reconfiguring the default gateway on tenant endpoints to the IP address of the service endpoint.

Further details on how it works are available [here](/how/README/).

Or jump right to the topic you want to learn more about.

### Topics

- [Routed Access Datacenter](/how/README/#routed-access-datacenter)   

- [Virtualization hosts](/how/README/#virtualization-hosts) 
 
- [VXLAN Tenant Isolation](/how/README/#vxlan-tenant-isolation)

- [Romana Tenant Isolation](/how/README/#romana-tenant-isolation)
  
- [IP Address Management](/how/README/#ip-address-managment)
  
- [Route Manager](/how/README/#route-manager) 
 
- [Service Insertion](/how/README/#service-insertion) 
 
- [Policy Based Control](/how/README/#policy-based-control)  

---

### Routed Access Datacenter

To completely understand the operation of Romana, it is important to first understand the basics of a layer 3 routed access datacenter design. 

Today's modern datacenters are frequently built using a Spine/Leaf network design. The details of this approach are beyond the scope of this document, but several good resources are available [here]( http://www.cisco.com/c/en/us/products/collateral/switches/nexus-5000-series-switches/guide_c07-673997.html), [here]( https://www.arista.com/assets/data/pdf/DesignGuides/Arista-Universal-Cloud-Network-Design.pdf), and [here](http://searchdatacenter.techtarget.com/feature/Data-center-network-design-moves-from-tree-to-leaf).

These kind of layer 3 designs are popular because of their simplicity and performance and are often used as the underlay network for cloud deployments. An important point to remember about these designs is that they can take advantage of [route aggregation]( http://www.linktionary.com/r/route_aggregation.html) to further simplify their design.

Briefly stated, route aggregation is simply organizing IP addresses and configuring routers in a way that is 'topologically significant'. That is, each part of the network can only contain addresses from a specific CIDR block. By organizing the addresses hierarchically, the number of routes needed to reach every endpoint is reduced. 

The diagram below shows how route aggregation makes all endpoints in the network reachable with just a few routes configured on the leaf and spine devices. It illustrates how a 10/8 network (with up to 16M IP addresses) could be partitioned across 2 Spines and 4 Leaves so that each Spine port forwards packets to one of four /10 networks (one on each Leaf) and each Leaf forwards packets to one of 64 ports configured with a /16 network.

![Route Aggregation](/images/Aggregation.png)

With this configuration, each Spine device is configured with the *same* 4 static routes, one to each Leaf device. Each Leaf device is configured with one route to each port. The routes on the Leafs are sequential and differ across Leaf devices only by an offset in the second octet of the CIDR.

The simplicity of this design is undeniable. It is compact, intuitive and predictable and delivers high performance as well. Which is why it is so popular among large data center operators and the basis for most cloud network underlay designs. 

One constraint imposed by a routed access design is that it *requires endpoint IP addresses to conform to the route aggregation hierarchy*. 

The obvious way to relax this constraint is to update the network devices with individual host routes to the specific endpoints that do not conform to the hierarchy. 

However, updating the devices with host routes introduces the added complexity of a route distribution protocol like BGP, which requires additional skills and training. Additionally, at the limit, injecting host routes, by whatever means, will eventually overflow the capacity of the devices.

[(^Back to Top^)](#topics)

---

### Virtualization Hosts

Allocating a /16 CIDR and 64K IP addresses to a single port does not make a lot of sense until you realize that the host attached to this port would actually be a virtualization host that could use these available IP addresses for local VMs or containers on the host.

For example, on the [Google Compute Engine]( https://cloud.google.com/compute/) IaaS platform, when you launch a Kubernetes VM, by default it is allocated a [complete /24 network]( http://kubernetes.io/v1.0/docs/admin/networking.html#how-to-achieve-this ) so that each pod launched would have one of the possible 255 IP addresses from within the assigned range. 

Using this as a simple baseline example, if you allocate a /24 range to each VM, having a /16 for the entire virtualization host would accommodate up to 255 VMs.

The diagram below extends the [Routed Access Datacenter](#routed-access-datacenter) design to the virtualization hosts where the host is configured as a router to forward traffic to VMs, where each VM have been allocated a /24 networks for their own use.

![Virtualization Hosts](/images/vHosts.png)

Configuring the host to route traffic is a standard feature built in to the Linux kernel. It simply has to be configured with the routes, just like a network device.

If an actual cloud datacenter were build using the assumptions from the previous example, the 16M IP addresses available in the 10/8 network would be allocated across 255 virtualization hosts, where each could support up to 255 VMs, with each VM supporting up to 255 IP addresses for local endpoints.

Alternative configurations are easily designed simply by changing how many address bit are assigned to network ports (i.e. max hosts supported). A /18 (10 bits) to each port (vs only 8 bits for /16 in previous example), would allow 2 more address bits available for ports supporting four times as many hosts (up to 1K, 2^8 vs 2^10), but each host could accommodate only one fourth the number of endpoints (2^16 v. 2^18).  Other variations include using only a portion of the complete 10/8 for smaller configurations, such as a 10/10 for 4M endpoints, a 10/11 for 2M endpoints, etc.

[(^Back to Top^)](#topics)

---

### VXLAN Tenant Isolation

The traditional method to maintain tenant isolation is to create individual layer 2 segments on top of a layer 3 underlay network. These overlay networks use VXLAN encapsulation (or similar encapsulation protocols) which supports up to 16M isolated network segments. And just like any other layer 2 segment, they are configured with a CIDR address range and gateways to routers that forward traffic to other VLANs and other networks, as necessary. 

Assigning a VXLAN to individual tenant network segments provide layer 2 VLAN style isolation for every segment. If a NAT service is also available, segments can also support overlapping IP addresses. 

While VXLAN technology is reasonably mature and stable, bandwidth and CPU performance overhead are often cited as major concerns.

The overlay requires a Virtual Tunnel Endpoint (VTEP) processor to add a header and to insert the correct field value for each packet that enters or exits the VXLAN. While line rate throughput can be achieved, the actual performance impact is [difficult to qualify](http://blog.ipspace.net/2015/02/performance-of-hypervisor-based-overlay.html). However, with header overhead [reducing bandwidth by ~6%](http://packetpushers.net/vxlan-udp-ip-ethernet-bandwidth-overheads/), and [CPU overhead taking at least 3%](http://chinog.org/wp-content/uploads/2015/05/Optimizing-Your-Virtual-Switch-for-VXLAN.pdf) the overall performance impact cannot be ignored.   

But more important to consider than these simple stand alone VXLAN performance benchmarks is the cumulative latency introduced by encap/decap cycles along the complete path. At a minimum, all east/west traffic requires an encap at the source and a decap at the destination, or one complete cycle. However, for all routed VXLAN and north/south traffic, there is an *additional* encap/decap cycle for each intermediate router and/or service functions. Additionally, since overlay networks remove all topology context from the network, inefficient packet paths are unavoidable.

For example, the diagram below shows the path packets take when OpenStack VMs on different tenant VXLAN segments communicate. There is an extra encap/dcap cycle for the router on a different host to forward the traffic to the proper endpoint.

If <10% performance loss were all it cost to run VXLAN, considering the benefits of segment isolation, it would still seem to be a bargain. However, from an operational perspective, there are other more critical challenges with running VXLANs than performance alone.

1. *Visibility*: VXLAN Encapsulation hides traffic inside of IP packets that can not be seen with standard network monitoring tools. Actual paths through the physical network are difficult to because endpoints inside VXLANs could be located anywhere. 

2. *Manageability*: VXLANs use a VLAN Network Identifier (VNID) to identify which VLAN the traffic belongs to. Managing these VNID is conceptually similar to managing VLAN ID, except instead of managing 4K ID, you need a system that can handle up to 16M. 

3. *Interoperability*: When running VXLAN, operators will invariably require VTEPs on visualization hosts, network devices and other VXLAN gateways. Each VTEP potentially could have different APIs, versions or come from different vendors.  There are no interoperability standards (or even best practices) for VTEPs, which makes coordinating VNIDs, even with a commercial Enterprise SDN solution, especially challenging.

4. *Cost*: To avoid the latency impact of encapsulation, new network devices that have hardware acceleration for VTEPs may be required. In addition, an SDN solution of some kind is necessary to coordinate VNIDs. There are also the operational burden of the integrating and managing the entire environment on an ongoing basis.

![OpenStack VXLAN Routing](/images/vxlan.png) 

Container orchestration systems such as Docker or Kubernetes also use VXLAN for segment isolation. Today, when these systems are run on OpenStack VMs, the result is *double* encapsulation! It will be a while before these conflicting deployment models are reconciled.

The net result of architectural incompatibility, operational challenges and performance overhead of running VXLANs have operators looking for better ways to provide secure tenant isolation.

[(^Back to Top^)](#topics)

---

### Romana Tenant Isolation 

From an operational perspective, what is needed is a multi-tenant network isolation model that avoids the complexity of overlay networks, while maintaining the simplicity of the routed access datacenter design.

The key insight that lets Romana achieve this operational goal is recognizing that [Cloud Native application](/cloud-native-sdn/) do not require layer 2 networks at all. For example, the [Kubernetes network model]( http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model) indicates only that:

> Kubernetes imposes the following fundamental requirements on any 
 networking implementation (barring any intentional network segmentation 
 policies):

>>* all containers can communicate with all other containers without NAT
>>* all nodes can communicate with all containers (and vice-versa)
without NAT
>>* the IP that a container sees itself as is the same IP that others see it as

Since Cloud Native, applications do not require layer 2 networks, Romana tenant isolation can avoid an overlay entirely, provided that isolation can be provided at layer 3.

Fortunately, this can be easily achieved by configuring IP tables rules on the hypervisor that allow only the endpoints in the same segment to communicate.

Maintaining these kinds of access control list (ACLs) for all endpoints is large and complex data management problem. The problem becomes even more difficult when more advanced traffic management policies are required [Is this true? How complex is this, really?].

To reduce this complexity and simplify the solution further, Romana extends the idea of capturing network topology in the IP address to also include a segment identifier.

Similar to the way the high order address bits identify a specific port in the network and assigning it its own /16 network, we can assigning another set of bits to identify individual segments. Each segment, therefore, has its own unique CIDR on every host.  

The power of this addressing scheme is that this segment CIDR represents a handle by which all traffic in and out of the segment can be controlled. Not only for ACLs, but also for any kind of traffic management policy that needs to be applied to the segment.

This approach can be further extended to split the segment identifier field in to a tenant ID and a segment ID, which creates Tenant CIDRs as well as Tenant Segment CIDR within the tenant range.

The diagram below illustrates how an IP address can be partitioned to capture host, tenant and segment identity. Note, for simplicity, the example uses 8 bits for each range which makes the resulting IP addresses readable in four octet dot address notation. 

![Route Aggregation](/images/cidr.png)

Note that all traffic for Host 1 (Host ID = 54), would go to the 10.54/16 network. Of this, traffic to Tenant 1 (ID=5) would be directed to the 10.54.5/24 network. Traffic for Tenant 2 (ID=22) would go to 10.54.22/24. Traffic to Tenant 1 Segment 1 (ID=12) would go to 10.54.5.192/20 (12 in bits 25-28 of address, or 1100000=192), and so on.  These CIDRs represent handles by which traffic in to these segment can be steered and controlled by any physical layer 3 device.

Individual endpoints that are within segments would have IP addresses from the CIDR range of the segment. VM1 (ID=11) on Tenant 1 Segment 1 would get 10.54.5.203 (192+11=203).

Allocating high order bits of an address to map to network ports is a standard part of hierarchical routed access datacenter designs. Romana brings this kind of 'topology significant' addressing  to application endpoints,  which enables traffic to these endpoints to be organized, aggregated and controlled just like all layer 3 traffic on the network.

For each Hosts, there is CIDR that identifies traffic to the entire host, each tenant on the host, and for each tenant, the traffic to each segment. Across all hosts, the total number of CIDRs is the total number of Routes on each Host, times the number of hosts deployed.  I

It is the job of the [Router Manager](#route-manager) to create, set, update and manage these routes across the entire network.


 [(^Back to Top^)](#topics)

---

### IP Address Management

Extending [topologically significant](#romana-tenant-isolation) addressing to application endpoints is a powerful tool to simplify cloud networking. The remaining problem is ensuring that IP addresses are assigned in a way that conforms to this design.

Romana does this with it own IP Address Management system that maintains a list of all Tenants and the network segments they request. For OpenStack, Romana listens to the message queue and whenever a new Tenant or Segment is created, updates it database. 

When a new VM is launched, Romana listens on the Message Queue to learn which Host will get the VM, then based on the Host ID, Tenenat ID and Segment ID calculates the correct IP address, and forwards it to the standard OpenStack DHCP mechanism for it to be pulled and assigned to the VMs network interface.

The actual details of how this is done is described in more detail in the documentation, but conceptually, a

[(^Back to Top^)](#topics)

---

### Route Manager

Romana includes a Route Manager that communicates with the Host to configure a new tap interface and a host route (/32) for every new VM that is launched.

[(^Back to Top^)](#topics)

---

### Microservices

A powerful advantage of the Romana layer 3 traffic control system is that it is inherently service oriented. Recognizing that network Segments define a Service boundary, the CIDR that controls traffic in and out can be use to steer traffic between Microservices.

This kind of traffic steering can be done by the application developer as they normally would using some kind of Service Discovery service to directly get the IP address of a service endpoint. 

Alternatively, operators have

### Service Insertion

Having individual CIDRs that identify traffic Romana can insert Service Functions into traffic flows 
In addition, when configuring endpoints on this segment, service chaining can easily be achieved by specifying the next hop router to be the IP address of the next Service Function in the chain. See section [Service Insertion](#service-insertion) for more details on how this works.

[(^ Back to Top^)](#topics)

---

### Policy Based Control
The policy controller

[(^Back to Top^)](#topics)



