---
layout: page
title: Cloud Native Architectural Style 
nav_text: Cloud Native SDN
menu_text: Cloud Native Architecture
icon: cloud
firstnav: 3
secondnav: 3
permalink: /cloud/cloud_native_arch/

---

According to the [Cloud Native Foundation](https://cncf.io/) Cloud Native application can be built using a variety of technologies, but they share a number of following important [characteristics](https://cncf.io/about/charter):

>Container packaging. Running applications and processes in software containers as an isolated unit of application deployment, and as a mechanism to achieve high levels of resource isolation. Improves overall developer experience, fosters code and component reuse and simplify operations for cloud native applications.
<br><br>
Dynamically managed. Actively scheduled and actively managed by a central orchestrating process. Radically improve machine efficiency and resource utilization while reducing the cost associated with maintenance and operations.
<br><br>
Micro-services oriented. Loosely coupled with dependencies explicitly described (e.g. through service endpoints). Significantly increase the overall agility and maintainability of applications. 

The  micro-service orientation is important from a networking perspective because loosely coupled service endpoints impose very few requirements on the network, other than simple IP reachability. Beyond this, responsibility for administering certain network services is delegated to application developers.  

This simple reachability model is important because it avoids the complexity of extending layer 2 networks across the datacenter (and into the cloud) and enables seamless and transparent scheduling of containers across cloud boundaries, greatly simplifying hybrid cloud deployment. 
