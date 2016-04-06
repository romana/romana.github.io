---
layout: page
title: Romana Network Automation
nav_text: NA
menu_text: Network Automation
firstnav: 3
secondnav: 2
permalink: /cloud/network_automation/

---
Romana is a new network and security automation solution specifically designed for [Cloud Native](/cloud/cloud_native_arch) applications. 

Romana is unlike traditional Software Defined Networking (SDN) solutions because it focuses on automation of standard network and security elements and does not rely on overlay virtual networking. As a result, Romana Cloud Native Networks are less expensive to build, easier to operate and deliver higher performance than multi-tenant cloud networks built using traditional Software Defined Networking.

---

### Network and Security Automation v. SDN

To compare how Romana's Network Automation and Security solution differs from traditional Enterprise SDN it is helpful to look at the objectives of their design.

The goal of Enterprise SDN was to virtualize enterprise networks in their entirety, enabling datacenter and legacy applications to run unchanged in virtualized environments. 

The canonical example of Enterprise SDN is [VMware NSX](https://www.vmware.com/products/nsx). The defining characteristic is the use of overlays and VTEPs to build isolated layer 2 virtual networks. Some implementations go further with efforts to virtualize many layer 3 and layer 4-7 functions.

This is very different from the requirements for Cloud Native applications, which rely on modern DevOps automation for Continuous Integration and Continuous Deployment (CD/CD).  

Without the Enterprise SDN requirements, Romana can focus on the automation of network provisioning and security and avoid the complexity that traditional SDN solutions introduce, such as VXLAN overlays for enterprise virtual networks. 

---

### Romana Network and Security Automation

Romana Network and Security Automation is simpler than Enterprise SDN because the requirements are simpler. Since endpoints do not need layer 2 adjacency, the complexity of managing overlays and tunnel endpoints disappears. This is especially important since the challenges of building and operating overlays for network virtualization are [well known](/how/background/#vxlan-isolation/). 

However, since VXLAN based overlay networks provide segment isolation and facilitate multi-tenancy, without VXLAN Romana needs to implement multi-tenancy [differently](/how/romana_details/#romana-tenant-isolation). 

Romana provides this multi-tenancy without an overlay with an innovative [layer 3 based tenancy model](/how/romana_details/#romana-tenant-isolation). It lets operators build Cloud Native Networks directly on the physical network, which delivers higher performance and provides better visibility, manageability and control. And since no traffic is encapsulated, all standard layer 3 traffic steering and filtering techniques can be used (in physical or virtual form factors) further simplifying network operations.

Learn more about how Romana works [here](/how/romana_basics/)

---
