---
layout: post
title: Romana Network Services Platform
date: 2015-10-1
categories: 
- cloudnative

---

* [Romana Network Controller](#romana-network-controller)  
* [Romana Web Designer](#romana-web-designer) 
* [Romana Services Platform](#romana-services-platform) 
* [Network Management Applications](#network-management-applications) 
* [IP Address Manageent](#ip-address-management) 
* [Network Address Translation](#ip-network-address-translation) 
* [Service Registration and Discovery](#ip-service-registration-and-discovery) 
* [Load Balancing](#loadbalacing) 
* [Firewall](#firewall) 

[Romana Addressing](#romana-addressing)  

* [Platform Services Space](#platform-deployment-space) 
* [Platform Storage Space](#platform-storage-space)
* [Platform Deployment Space](#platform-deployment-space) 

[Deployment Considerations](#deployment-considerations)  
[Provider Networks](#provider-networks)  
[Policy Based Security and Network Access](#policy-based-security-and-network-access)


On top of this L3 network architecture Roman has built a complete Network Serices Platform (NSP) that includes a variety of  network Service Functions (SFs) including IP Address Management (IPAM), Firewalls, Load Balancers, NAT and Service Registration and Discovery (DNS-SD). See the Romana [Roadmap](/roadmap/)for the availability of these and other Service Functions.

These network services are all consumable directly by tenants, relieving application developers of the burden of creating, maintaining and operating them themselves.  In addition, Romana also enables operators to establish system wide network and security policies for all compute and storage resources within the platform. 

Romana supports OpenStack IaaS, standalone virtualization hosts and Amazon's EC2 VPC as well as all Docker and Rocket container management systems.


Romana is a new Software Defined Network (SDN) solution specifically designed for the unique requirement of Cloud Native applications.


By eliminating encapsulation and avoiding Neutron routers on remote hosts, Romana delivers over six times faster packet response times for common OpenStack traffic patterns. By avoiding the additional encapsulation required for running Kubernetes or Docker networking on OpenStack, even greater performance gains can be delivered.
Since no special kernel modules are hypervisor modifications are required, Romana works eually well in the cloud when running containers on instances in EC2.

Romana's uniform layer 3 approach allows network functions to be inserted easily for traffic management and enables hybrid cloud deployment by allowing container orchestrations systems to transparently scale capacity across private and public clouds worldwide



***

## Romana Overview
The Romana System consists of the Romana Network Controller, Romana Network Designer, the Romana Services Platform and a suite of network management applications that simplify building, securing and operating multi-tenant cloud networks.  Romana enables isolated tenant network segments to be deployed securely across the physical infrastructure including hosts, racks, data centers and even third party cloud IaaS providers.  Romana network segments can extend across OpenStack and EC2 VPC environments, as well as into virtualized and Docker/Rocker container hosts.  Microservices are easily built around these network segments by including load balancers behind service endpoints, which can then be registered with the Romana Services Platform for system-wide discovery and re-use. 
 
Romana network management applications include IP address management (IPAM), Network Address Translation (NAT), Load Balancing (LB), Firewall  (FW) and Service Discovery (DNS-SD).

### Romana Network Controller
The Romana Network Controller provides the global view of the Romana Network and provides a Northbound interface that interacts with the [Romana Network Management Applications] (#network-management-applications) and a Southbound interfaces to control IaaS  and Container Management systems. 

The Controller is fully distributed across all data centers with no single points of failure and uses a distributed key-value store to maintain configuration state. 

See [Issue #4] (https://github.com/romanaproject/romana/issues/4) for current details.

***

### Romana Web Designer
The Romana Web Designer is an interactive web based data center/cloud network design tool that simplifies the specification and configuration of Romana networks. The Web Designer is availble at designer.romana.io where users may create new accounts and design their networks. In addition, if the Network Contoller is already running on premesis, the Wed Designer is availble by accessing the controller locally as well. 

Romana networks use a L3 routed access design where route aggregation is used to simplify network deployment and ongoing operations. 

The Romana Designer requires specification of various addressing parameters including:

* Platform elements such as number of data centers, VPC, etc
* Maximum number of physical hosts within each platform element
* VLAN ID and available address range (10/8 recommended)
* Number of leaf switches available (number of racks)
* Number of spine switches available
* Number of links from hosts to leaf switches
* Number of links to be configures between leaf and spine
* Maximum number of tenant network Segments
* Maximum number of allowable VM per segment per host
* Characteristics of network links among platform elements.

From this input data, Designer produces an output configuration template for all spine, leave and host network configurations. In addition, a the interactive tool provides a hierarchical network graph that displays network topology for both the physical network and tenant environments as well as operational statistics and performance metrics.  See [Romana Addressing] (#romana-addressing) for details on how uses this design along with intelligent IP address management to simplify cloud networking

Designer network specifications and output configurations are also required for the Romana IPAM and other network applications.

See [Issue #5] (https://github.com/romanaproject/romana/issues/5) for details.


### Romana Services Platform

A description of the Servies Platrform

### Network Management Applications

The Romana suite of network management applications includes 

#### IP Address Management

IPAM is a critical network applcation that.

#### Network Address Translation

Network Address Translation is necessary for all North-South traffic as well as gaining access to all Romana tenant networks.

#### Service Registration and Discovery

Service discovery is a new platform requirement that cloud native applications have come to rely upon.

#### Firewall

#### Load Balancing


***
## Romana Addressing

Central to Romana's approach to simplify cloud computing networks is the introduction of a new L3 based tenancy model. L2 VLAN based tenancy models, while simple and fast, are limited in scale. Overlay networks address the scalability problem, but introduce significant complexity and adversely impact performance and manageability. Like other efforts that provide L3 tenant isolation, Romana's L3 tenancy model enforces isolation on the host by configuring Access Control Lists (ACLs) either directly with iptables, or indirectly through Open vSwitch.

However, unlike these other L3 approaches, ACLs are applied at a segment level, not a VM level. This has the advantage of requiring only one ACL per segment vs. one rule per VM. This dramatically simplifies configuration and improves performance.

Romana achieves this L3 based, segment level isolation through intelligent assignment of IP addresses. By imbedding a unique segment identifier into the IP address of the VM the host can apply an ACL for an entire segment simply by allowing or denying access to the segment.

Cloud data center networking is further simplified by aggregating routes to all VMs into a single route to the host. This is achieved by assigning to each host its own network ID out of a full 10/8 CIDR block. The results in a L3 datacenter access design that is trivially simple, where physical infrastructure can be configured and maintained without complex routing protocols, controllers or overlays.

IP addresses for individual VM are generated by the Romana IPAM system which specifies values for the physical host that is running the VM (Host Network ID, or simply Host ID), a Segment ID and a VM ID. Together, these fields, along with the 10/8 network ID bits make up the full 32 bit IPv4 IP address.

For example, the illustration below shows how an IP address would be generated and assigned to a VM running on host where the system was built out to accommodate up to 64 hosts, 32K isolated segments with a maximum of 8 VM per segment on each host.

![Address Example](AddressingExample.png)

The length of the Host ID, Segment ID and VM ID are all configurable via the Romana Cloud Designer which allows for a variety of cloud tenancy options. The table below illustrates how allocating more bits to the Host ID would increase the number of supported hosts, but reduce the total number of isolated segments. 

![Bit Allocation](BitAllocation.png)

The Romana Designer allows each of these fields to be set and calculates the limits of the various aspects of the cloud network design.


### Platform Services Space

Romana accommodates each of the available hosts to be designated as special purpose hosts. While most hosts are built to run tenant virtual machines, there is also the need for the cloud computing platform to provide common network and storage services to tenants.  For example, Storage resources can be configured on dedicated hosts and all routes to these nodes can be configured QoS rules and high performance NICs. Likewise, Network functions including NAT, DNS  and Firewall can be deployed in dedicated Service Nodes with Virtual Network Function (VNF) appliances that deliver these functions.
The Romana addressing scheme easily accommodates this by allowing one or more host IDs to be reserved for platform service nodes. 

When services nodes are all located in a single rack, they can take advantage of route aggregation, otherwise (physical) host routes would be required. Co-located Service Nodes have the additional advantage of being more easily configured with specific network and/or security policies such as stateful firewalling and QoS.

### Platform Storage Space

### Platform Deployment Space

Romana uses its intelligent network address scheme to partition physical resources. This is done by assigning each their own network address out of the Romana address space. For example, when a cloud computing deployment is spread across multiple data centers, each can be assigned its own network that can then be further partitioned across local spine, leaf and host resources.
Third party cloud computing resources, such as Amazon's EC2 Virtual Private Cloud, can also be assigned their own network as well. Individual cloud deployment technologies, such as OpenStack, Docker  and standalone virtualization Hosts and even bare metal servers  can also be assigned their own network to seamlessly interoperate within the Romana environment.
***
## Deployment Considerations

Although there no strict requirement for a complete 10/8 CIDR block, having all 24 bits of address space allows for an enterprise-sized private cloud.  The alternative to this is to assign all Romana cloud resources to a single VLAN supporting the complete 10/8 range. 
This will require NAT in and out of tenant Segments to existing data center resources, but this is generally low volume East-West traffic. North-South traffic, as is typical, requires NAT to Floating IP Addresses.
***
## Service Chaining and Insertion
***
## Provider Networks

Romana also provides direct access to Tenant environments [what good is this??]
***
## Docker Ambassador
Something on how to support Ambassador in the platform.

## Policy Based Security and Network Access

The L3 tenancy model, along with the routed access design of Romana infrastructure facilitates explicit physical segregation of cloud resource Spaces (link: Platform Spaces). For example, 
9. Platform Services
1.2 Network Address Translation
1.3 Firewall
1.4 DNS-SD
1.5 etcd as a Service EaaS 
When Romana is deployed on a complete IaaS platform distributed key-value store service
1.6 
10. Network Management Applications
1.7 IP Address Management
1.8 Tenant Topology Manager



 
