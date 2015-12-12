---
layout: page
title: Romana
firstnav: 2
secondnav: 1
permalink: /how/romana/
---

### Topics

- [Introduction](/how/romana/#introduction)
- [Romana Tenant Isolation](/how/romana/#romana-tenant-isolation)
- [IP Address Management](/how/romana/#ip-address-management)
- [Host Agent and Route Manager](/how/romana/#host-agent-and-route-manager) 
- [Microservices](/how/romana/#microservices) 
- [Service Insertion](/how/romana/#service-insertion) 
- [Policy Based Control](/how/romana/#policy-based-control)  

---

### Introduction

To better understand the operation of Romana, it is helpful to also understand the basics of a layer 3 routed access datacenter design, tenant isolation and service chaining and insertion. A breif summary of these concepts is available [here](/how/background/)

---

### Romana Tenant Isolation

The traditional method to maintain tenant isolation is to create individual layer 2 segments on top of a layer 3 underlay network. A summary of this overlay approach is described [here](/how/background/)

What operators need is a solution that supports a multi-tenant network isolation model that avoids the cost and complexity of overlay networks, while maintaining the simplicity of the routed access datacenter design.

Romana Cloud Native SDN delivers this exact solution. 

The key insight that lets Romana achieve this operational goal is recognizing that [Cloud Native](/cloud-native-sdn/) applications do not require layer 2 networks at all. For example, the [Kubernetes network model]( http://kubernetes.io/v1.0/docs/admin/networking.html#kubernetes-model) indicates only that:

> Kubernetes imposes the following fundamental requirements on any 
 networking implementation (barring any intentional network segmentation 
 policies):<br><br>
* all containers can communicate with all other containers without NAT<br>
* all nodes can communicate with all containers (and vice-versa)
without NAT<br>
* the IP that a container sees itself as is the same IP that others see it as

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
 
|Level|ID|CIDR|
|Host|1|10.1/16 |
|Tenant|1|10.1.1/24|
|Segment|1|10.1.1.16/28|
{: class='romanatable'}

Across all hosts, the total number of CIDRs is the total number of routes on each Host, times the number of hosts deployed. It is the job of the Romana [Route Manager](#route-manager) to create, set, update and manage these routes across the entire network. [details TBD]

{% include backtotopbutton.html %}
---

### IP Address Management

Attaching [application level context](#romana-tenant-isolation) to endpoint addresses is a powerful tool to simplify cloud networking. The remaining problem is ensuring that IP addresses get assigned in a way that conforms to this design.

Romana does this with its own IP Address Management system what works with cloud orchestration systems such as OpenStack and Kubernetes and maintains a list of all Tenants and the network Segments and endpoints they create. 

For OpenStack, Romana provides a standard OpenStack ML2 plugin. Whenever a new Tenant, Segment or endpoint is created, Romana updates its database. 

When a new VM is launched in OpenStack, Romana learns which Tenant is launching it and the segment on which they wish it to be placed. When OpenStack identifies the host for the VM, the Romana IPAM calculates the IP address based on the Host ID, Tenant ID and Segment ID.  This address is then assigned to the VM interface using DHCP. mechanism.

For other cloud orchestration systems, such as Kubernetes, a similar process takes place to make sure all application endpoints are assigned the proper addressees.

{% include backtotopbutton.html %}
---

### Host Agent and Route Manager

Romana uses the native routing functions in the Linux kernel for packet forwarding and does not require any additional kernel software (dataplane kernel modules, etc.). Configuring routes on the Host is done with standard linux ip route commands, which are issued locally via the Romana Host Agent. 

Before any endpoints can be configured, the Route Host Agent must first configure the default gateway interface on the host that all local endpoints use. When a new endpoint is launched on a host, the Host Agent to configure a route. The routes are simply the default route to the host gateway. 

However, each route can be reconfigured with a different default gateway to direct traffic to an alternative device for [Service Insertion](#service-insertion) or to deal with [VM migration](github issue). Such special routes are maintained by the Romana Route Manager, which communicates with the Romana Host Agent on one or more hosts, as needed.

{% include backtotopbutton.html %}
---

### Microservices

A powerful advantage of the Romana layer 3 traffic control system is that it is inherently service oriented. Recognizing that network Segments define a Service boundary, the CIDR that controls traffic in and out can be used to steer traffic between Microservices.

In addition, the Romana CIDR also has service context that can be used to control how policies are to be applied to the traffic that reaches a service. Tenant and Segment isolation is just one example of how Romana can apply policy to network traffic throughout the system.

The default gateway of the endpoints in a Segment (i.e. within a Microservice) can be assigned to a Load Balancer, or any other Service Function device to act as the service endpoint. These front end Service Functions can apply any kind of user policy that is required. 

This kind of front end Service Function is just one example of the more general Service Insertion capability of Romana.

{% include backtotopbutton.html %}
---

### Service Insertion

Connecting different Microservices to compose more complicated applications is typically done directly by the application developer by naming service endpoints and adding them to a Service Discovery registry. When services need to communicate, they look for the named service in the registry to learn the IP address (or addresses) where the service is available, and open a connection directly with the service endpoint. The path that traffic actually takes between service endpoints is generally not something developer cares about. 

Operators, on the other hand, frequently want to steer traffic through different paths because of different policy objectives (Security, Encryption, etc.).  These traffic policies can not be left to the developer to apply, which means operators need a way to transparently steer traffic through Service Functions, independent of how individual microservices ordinarily communicate.

Since Romana has individual CIDRs that identify traffic flows, it can insert Service Functions along the path simply by specifying the next hop router to be the IP address of the next Service Function in the chain. The Route Manager already does when it sets up a route. Modifying the route in this way is straightforward. All that is needed is a way to define the path.

Defining these traffic path is the job of the [Romana Service Policy Manager](#policy-based-control).

A more detailed overview of Service Insertion is available [here](/how/chain/)

{% include backtotopbutton.html %}
---

### Policy Based Control

{% include backtotopbutton.html %}



