---
layout: page
title: Romana Details
menu_text: More Details
nav_text: NA
firstnav: 2
secondnav: 2
permalink: /how/romana_details/
---

### Topics

- [Introduction](/how/romana_details/#introduction)
- [Romana Tenant Isolation](/how/romana_details/#romana-tenant-isolation)
- [IP Address Management](/how/romana_details/#ip-address-management)
- [Route Manager and Host Agent](/how/romana_details/#route-manager-and-host-agent) 
- [Microservices](/how/romana_details/#microservices) 
- [Service Insertion](/how/romana_details/#service-insertion) 
- [Policy Based Control](/how/romana_details/#policy-based-control)  

---

### Introduction

To better understand the operation of Romana, it is helpful to also understand the basics of a layer 3 routed access datacenter design, tenant isolation and service chaining and insertion. A brief summary of these concepts is available [here](/how/background/).

Romana Networks are built and managed by a set of configuration and control services that are integrated into popular Cloud Management Systems (CMS) including OpenStack, Kubernetes and Docker. 

Application developers using these systems see no changes to their standard development processes. An operator deploying one of these CMS's in their own datacenter would build it in as part of their private cloud deployment. When running Docker or Kubernetes in AWS, users would deploy and configure Romana as part of the CMS installation.

Conceptually, Romana provides two important functions: 

* An *IP Address Management* (IPAM) system to ensure endpoints are assigned addresses in a way that maintain the simplicity of a layer 3 [routed access datacenter](/how/background/#routed-access-datacenter)   design, and  

* A *Route Manager* to configure and update virtualization hosts with routes to these endpoints. The Route Manager also configures the host with firewall rules to provide tenant isolation and to enforce other traffic management and control policies.

The implementation details of these functions are provided in the architectural overview available [here](www.github.com/romana/core).

Together, intelligent assignment of IP addresses along with route control lets operators build multi-tenant cloud networks without an overlay network for tenant isolation. 

---

### Romana Tenant Isolation

The traditional method to maintain tenant isolation is to create individual layer 2 segments on top of a layer 3 underlay network. A summary of this overlay approach is described [here](/how/background/#vxlan-tenant-isolation/).

What operators need is a solution that supports a multi-tenant network isolation model that avoids the cost and complexity of overlay networks, while maintaining the simplicity of the routed access datacenter design.

Romana can deliver this solution because it focuses on the requirements of [Cloud Native](/cloud/cloud-native-sdn/) applications and these applications' network requirements are very simple. 

For example, the [Kubernetes network model](http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model) requires only reachability among endpoints, without NAT:

> Kubernetes imposes the following fundamental requirements on any 
 networking implementation (barring any intentional network segmentation 
 policies):<br><br>
* all containers can communicate with all other containers without NAT<br>
* all nodes can communicate with all containers (and vice-versa)
without NAT<br>
* the IP that a container sees itself as is the same IP that others see it as

Since Cloud Native applications do not require layer 2 networks, Romana can avoid an overlay network for tenant isolation as long as isolation can be enforced at layer 3.

Fortunately, this can be achieved with a layer 3 firewall by configuring IP tables rules on the hypervisor that allow communication only among valid endpoints. A local Romana agent is available to configure these firewall rules as needed. 

However, maintaining these kinds of firewall rules and access control list (ACLs) for all endpoints is a large and complex data management problem. The problem becomes even more difficult when more advanced traffic management policies are required [Is this true? How complex is this, really?].

To reduce this complexity and simplify the solution further, Romana extends the idea of capturing network topology in the IP address to also include a segment identifier in the address as well.

Similar to a routed access datacenter design where the high order address bits identify a specific port in the network, Romana can assign a set of bits to identify individual segments. Each segment, therefore, has its own unique CIDR on every host.  

The power of this addressing scheme is that this segment CIDR now has *application level context*, which Romana uses to simplify the firewall rules. Also, in addition to the CIDR being the handle by which all traffic in and out of the segment can be controlled, capturing this application level context in the CIDR makes *every layer 3 control device available for application level policy enforcement*.

Specifically, Romana assigns a set of bits to be a segment identifier and further splits it in to a Tenant ID and a Segment ID. This creates Tenant CIDRs as well as Tenant Segment CIDRs within the tenant range, each with their own application level context.

The diagram below illustrates how an IP address can be partitioned to capture host, tenant and segment identity. Note, for simplicity, the example uses 8 bits for each range, which makes the resulting IP addresses readable in four octet dot address notation. 

In this example all traffic for Host 1 (Host ID = 1), would go to the 10.1/16 network. Of this, traffic to Tenant 1 (ID=1) would be directed to the 10.1.1/24 network. Traffic for Tenant 2 (ID=2) would go to 10.1.2/24. Traffic to Tenant 1 Segment 1 (ID=1) would go to 10.1.1.16/28 (1 in bits 25-28 of address, or 00010000=16). For Host 2, all CIDRs would be the same, except that 1 would be replaced by 2, the ID of Host 2.

![Route Aggregation]({{ site.baseurl }}/images/cidr.png)

Endpoints that are in a specific segment would have IP addresses from the CIDR range of the segment. VM1 (ID=11) on Host 1 for Tenant 1 Segment 1 would get 10.1.1.27 (16+11=27).

Romana assigning addresses to application endpoints within the Tenant and Segment CIDRs enhances them with application level context. This context is used directly on the host to provide Tenant and Segment isolation. 

Segment isolation is just one example of how Romana uses application level context to simplify multi-tenant cloud networking in the same way that 'topology significant' addressing simplified data center design and operations.

In summary, for each virtualization host, there is a CIDR that identifies traffic to the entire host; a longer CIDR that identifies traffic to each tenant on the host; and for each tenant, there is an even longer CIDR that identifies traffic to each segment. Each CIDR captures important application level context. 
 
|Level|ID|CIDR|
|Host|1|10.1/16 |
|Tenant|1|10.1.1/24|
|Segment|1|10.1.1.16/28|
{: class='romanatable'}

Across all hosts, the total number of CIDRs is the total number of routes on each Host, times the number of hosts deployed. It is the job of the Romana [Route Manager](#route-manager) to create, set, update and manage these routes across the entire network. [details TBD]

Layer 3 firewalls avoid the use of overlays for tenant isolation and with Romana inserting tenant and segment IDs in IP addresses, isolation and traffic control can be simplified because they each have their own CIDR, which lets devices be configured more easily. 

{% include backtotopbutton.html %}
---

### IP Address Management

Attaching [application level context](#romana-tenant-isolation) to endpoint addresses is a powerful tool to simplify cloud networking. The remaining problem is ensuring that IP addresses get assigned in a way that conforms to this design.

Romana does this with its own IP Address Management system what works with cloud orchestration systems such as OpenStack and Kubernetes and maintains a list of all Tenants and the network Segments and endpoints they create. 

For OpenStack, Romana provides a standard OpenStack ML2 plugin. Whenever a new Tenant, Segment or endpoint is created, Romana updates its database. 

When a new VM is launched in OpenStack, Romana learns which Tenant is launching it and the segment on which they wish it to be placed. When OpenStack identifies the host for the VM, the Romana IPAM calculates the IP address based on the Host ID, Tenant ID and Segment ID.  This address is then assigned to the VM interface using DHCP.

For other cloud orchestration systems, such as Kubernetes, a similar process takes place to make sure all application endpoints are assigned the proper addressees.

{% include backtotopbutton.html %}
---

### Route Manager and Host Agent

Romana uses the native routing functions in the Linux kernel for packet forwarding and does not require any additional kernel software (dataplane kernel modules, etc.). Configuring routes on the host is done with standard linux ip route commands, which are issued locally via the Romana Host Agent. 

Before any endpoints can be configured, the Route Manager, via the Host Agent, must first configure the default gateway interface on the host that local endpoints use. When a new endpoint is launched on a host, the Route Manager instructs the Host Agent to configure a route. Normally, these routes are simply default routes to the host gateway. 

However, when a service is inserted into the path of an endpoint, this default route can be reconfigured to use a different address to forward traffic to a Service Function for [Service Insertion](#service-insertion) or to deal with [VM migration](github issue). 

{% include backtotopbutton.html %}
---

### Microservices

Cloud Native applications frequently take advantage of a [microservices](http://thenewstack.io/best-practices-for-developing-cloud-native-applications-and-microservice-architectures/) based approach. This requires replicated elements within a service be identified individually, yet all be managed collectively as a unified service. These requirements are well suited to a layer 2 networking model since it provides network isolation as well as explicit access control and membership affinity through its gateway and CIDR.

Other cloud networking solutions that use layer 3 for tenant isolation are unable to take advantage of these layer 2 properties. However, since Romana *does maintain CIDRs for each tenant segment*, like layer 2 networks, it is inherently service oriented because network segments can define microservice boundaries. And, since network segments define a service boundary, the CIDR that controls traffic in and out of a segment can also be used to steer traffic among Microservices and through operator defined [Service Functions](#service-insertion).

The default gateway of the endpoints in a segment (i.e. a microservice) can be assigned to a Load Balancer, or any other Service Function device to act as the service endpoint. These front end Service Functions can apply any kind of user policy that is required. 

Since an overlay is not necessary for Romana segments, all existing (physical or virutal) layer 3 devices can become Service Functions and/or microservice endpoints. This kind of front end Service Function is just one example of the more general Service Insertion capability of Romana.

{% include backtotopbutton.html %}
---

### Service Insertion

Connecting different microservices to compose more complicated applications is typically done directly by the application developer by naming service endpoints and adding them to a Service Discovery registry. When services need to communicate, they look for the named service in the registry to learn the IP address (or addresses) where the service is available, and open a connection directly with the service endpoint. The path that traffic actually takes between service endpoints is generally not something developer cares about. 

Operators, on the other hand, frequently want to steer traffic through different paths because of different policy objectives (Security, Encryption, etc.).  These traffic policies can not be left to the developer to apply, which means operators need a way to transparently steer traffic through Service Functions, independent of how individual microservices ordinarily communicate.

Since Romana has individual CIDRs that identify traffic flows, it can insert Service Functions along the path simply by specifying the next hop router to be the IP address of the next Service Function in the chain.

The Route Manager already does when it sets up a route. Modifying the route in this way is straightforward. All that is needed is a way to define the path.

Defining these traffic path is the job of the [Romana Service Policy Manager](#policy-based-control).

A more detailed overview of Service Insertion is available [here](TBD on Github) 

{% include backtotopbutton.html %}
---

### Policy Based Control

{% include backtotopbutton.html %}



