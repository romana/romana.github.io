---
layout: post
title: Layer 2 Network Deployment 
categories:
- blog
permalink: /blog/layer-2-networks/
---

Installing Romana on simple layer 2 network is, by far, the most common deployment option in use today. Romana from the very start has supported this configuration. This network topology relies on layer 2 broadcast traffic to learn about the other nodes on the network. 

Since every node already knows about all the other nodes, Romana can simply install routes to every other node via the local Romana agent and no route distribution software is necessary. The Romana agent pulls the routes from etcd that gets updated by the Romana controller when Kubernetes pod creation and network policy events occur. 

[Diagram Here]

Today, with the release or Romana v1.5, layer 2 deployments can now take advantage of topology aware IPAM (TA-IPAM) to let orchestration systems extend VLANs into container deployments.

For example, it is common for datacenter networks to provision dedicated VLANs for different teams or projects. For performance and/or security reasons there could be *test*, *qa*, *production* and *admin* VLANs that are isolated by core router ACLs.

Topology aware IPAM lets orchestration systems schedule resources directly on these VLANs, extending the VLAN security model into container environments. Users may choose to deploy dedicated nodes on specific VLANs, or have nodes with interfaces to multiple VLANs for *dedicated node*, or *shared node* operation.

[Another Diagram]

Within each VLAN, Romana also provides a [segmentation model](/how/romana_details/#romana-tenant-isolation) for network isolation required for enforcing Kubernetes Network Policy across Namespaces.

Topology aware IPAM is a powerful new feature that lets users extend traditional VLAN isolation and security models into their container deployments. 
