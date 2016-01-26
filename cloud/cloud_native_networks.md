---
layout: page
title: Cloud Native Networks
nav_text: Cloud Native SDN
menu_text: Cloud Native Networks
icon: cloud
firstnav: 3
secondnav: 1
permalink: /cloud/cloud_native_networks/

---

Building and operating scalable, high-performance, multi-tenant cloud computing networks is extremely difficult.  One common approach is to deploy a virtual network using VXLAN overlays and a commercial [Enterprise SDN](/cloud/cloud_native_sdn/#sdn-flavors) controller.

These overlay-based virtual networks were designed to address the challenges of deploying virtual machines on an enterprise network and are ill-suited to the challenges of the emerging container-based and [Cloud Native architectural style](/cloud/cloud_native_arch). 

In addition to supporting the Cloud Native architectural style, Cloud Native Networks must run in popular public and private clouds including EC2 and OpenStack and accommodate an [exploding number of dynamic container endpoints]( http://events.linuxfoundation.org/sites/events/files/slides/cc15_mcguire.pdf). This requires automated provisioning via tight integration with container orchestration systems such as Docker and Kubernetes.  

These unique requirements, along with the desire for more flexible, hybrid cloud deployment alternatives demand a fresh approach to building Cloud Native Networks.

We call this new approach *Cloud Native SDN*.

Learn more about Cloud Native SDN and how it compares to other SDN designs [here](/cloud/cloud_native_sdn/).

