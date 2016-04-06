---
layout: page
title: Cloud Native Networks
nav_text: Cloud Native Nets
menu_text: Cloud Native Networks
icon: cloud
firstnav: 3
secondnav: 1
permalink: /cloud/cloud_native_networks/

---

Building and operating scalable, secure, high-performance multi-tenant cloud computing networks is extremely difficult.  One common approach is to deploy a virtual network using VXLAN overlays for micro segmentation and a commercial Enterprise SDN controller.

These overlay-based virtual networks were designed to address the challenges of deploying virtual machines on an enterprise network and are ill-suited to the challenges of the emerging container-based and [Cloud Native architectural style](/cloud/cloud_native_arch). 

In addition to supporting the Cloud Native architectural style, Cloud Native Networks must also run in popular public and private clouds including EC2, GCE and OpenStack and accommodate an [exploding number of dynamic container endpoints]( http://events.linuxfoundation.org/sites/events/files/slides/cc15_mcguire.pdf). This requires automated network provisioning and policy based security via tight integration with container orchestration systems such as Kubernetes and Docker.  

Romana was designed from the ground up to address these specific requirements.

Learn more about Cloud Native Networks and how then can be built and secured through automation and policy based control with Romana, [here](/cloud/network_automation/).

