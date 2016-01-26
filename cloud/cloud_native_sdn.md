---
layout: page
title: Romana Cloud Native SDN
nav_text: NA
menu_text: Romana Cloud Native SDN
firstnav: 3
secondnav: 2
permalink: /cloud/cloud_native_sdn/

---
Romana is a new Software Defined Network (SDN) solution specifically designed for the [Cloud Native architectural style](/cloud/cloud_native_arch). Romana Cloud Native Networks are less expensive to build, easier to operate and deliver higher performance than multi-tenant cloud networks built using alternative SDN designs. 

---

### SDN Flavors Emerge

It is easy to see how different flavors of SDN have emerged simply by looking at the objectives of their designs.

* *Enterprise SDN Objectives*: Virtualize Enterprise networks in their entirety, enabling datacenter and legacy applications to run unchanged in virtualized environments. The canonical example of Enterprise SDN is [VMware NSX](https://www.vmware.com/products/nsx). The defining characteristic of Enterprise SDN is the use of overlays and VTEPs to build isolated layer 2 virtual networks. Some implementations go further with efforts to virtualize many layer 3 and layer 4-7 functions.

* *Carrier SDN Objectives*: [Network Function Virtualization](http://searchsdn.techtarget.com/definition/network-functions-virtualization-NFV) (NFV) and automated service provisioning. [OPNFV]( https://www.opnfv.org/) is an industry wide effort to solve this problem with a purpose-built SDN controller and network orchestration platform. The defining characteristic of Carrier SDN is the ability to automate deployment of complex multi-tenant Service Functions in configurable chains, as part of a complete [Service Function Chaining Architecture](https://datatracker.ietf.org/doc/rfc7665/).

* *WAN SDN Objectives*: Classify and intelligently forward WAN traffic based on cost, performance and other policy constraints. Dynamically optimize WAN edge device configurations. Successful deployments deliver large cost savings which is driving rapid adoption of [SDN solutions](http://www.networkcomputing.com/networking/software-defined-wan-a-primer/a/d-id/1307047) for the WAN.

* *Cloud Native SDN Objectives*: Automated, developer oriented provisioning of isolated multi-tenant [Cloud Native Networks](/cloud/cloud_native_networks/). Integrate with cloud orchestration systems across public and private clouds managing security and access control. Support grouping of endpoints into services and adding discoverable front-end service endpoints.

Clearly, the objectives for Cloud Native SDN are different from each of the other SDN designs and a separate solution is warranted. 

Specifically, there is no enterprise requirement for layer 2 networks, no WAN requirement for traffic classification or device reconfiguration, and no carrier requirement for NFV. 

Without these requirements, Cloud Native SDN can avoid much of the complexity these SDN solutions introduce, such as VXLAN overlays for enterprise virtual networks. 

---

### Cloud Native SDN

Cloud Native SDN is simpler than Enterprise SDN because the requirements are simpler. Since endpoints do not need layer 2 adjacency, the complexity of managing overlays and tunnel endpoints disappears. This is especially important since the challenges of building and operating overlays for network virtualization are [well known](/how/background/#vxlan-isolation/). 

However, since VXLAN based overlay networks provide segment isolation and facilitate multi-tenancy, without VXLAN, Cloud Native SDN needs to implement multi-tenancy [differently](/how/romana_details/#romana-tenant-isolation). 

Romana Cloud Native SDN provides this multi-tenancy without an overlay with an innovative [layer 3 based tenancy model](/how/romana_details/#romana-tenant-isolation). It lets operators build Cloud Native Networks directly on the physical network, which delivers higher performance and provides better visibility, manageability and control. And since no traffic is encapsulated, all standard layer 3 traffic steering and filtering techniques can be used (in physical or virtual form factors) further simplifying network operations.

Learn more about how Romana Cloud Native SDN works [here](/how/romana_basics/)

---
