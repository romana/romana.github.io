---
layout: page
title: Load Balancing and Romana VIPs
nav_text: Romana VIPs
menu_text: Romana VIPs
icon: build
firstnav: 1
secondnav: 4
permalink: /how/romana_vips/
---

Romana offers Kubernetes users running on premises with two new options to expose their services outside a cluster. On layer 2 LANs, they can configure services with a Romana VIP. On routed layer 3 networks, they can expose service IPs on multiple nodes and use MetalLB to configure routers to load balance traffic across the nodes.

### Romana VIPs

Kubernetes can use [ExternalIPs](https://kubernetes.io/docs/concepts/services-networking/service/#external-ips) to expose a service on node to make it available on the datacenter LAN. Although ExternalIPs are simple, they represent a single point of failure for the service and require manual allocation and configuration on the nodes. When there are many to configure, this can be tedious and prone to error. [Romana VIPs]( http://docs.romana.io/Content/advanced.html#romana-vips) are a solution to these problems.

Romana VIPs are defined by an annotation in a [service spec](https://raw.githubusercontent.com/wiki/romana/romana/files/nginx.yml). Romana then automatically brings up that IP on a node. Romana chooses a node with a pod running locally to avoid network latency within the cluster. When a node with a Romana VIP fails, Romana will bring up the VIP on a new node, providing failover for external services.

Romana VIPs are useful for exposing services that only need simple kubeproxy load balancing across pods. Romana VIPs can also be used to expose individual pods when a stable IP is required, such as Cassandra and other Big Data applications. Romana VIPs work in conjunction with [Romana DNS]( https://github.com/romana/romana/wiki/Romana-dns), which can be deployed as a service discovery mechanism for individual pods exposed outside of a cluster.

### Example configuration and yaml file

The example below shows a RomanaIP (192.168.99.101) configured on a node for the nginx service by adding the `romanaip` annotation to the spec.

    ...
    kind: Service
    metadata:
      name: nginx
      annotations:
        romanaip: '{"auto": false, "ip": "192.168.99.101"}'
    ...


The complete service spec is available [here](https://raw.githubusercontent.com/wiki/romana/romana/files/nginx.yml).

### Layer 3 Load Balancing with MetalLB

Romana works with [MetalLB]( https://metallb.universe.tf/) to configure routers to use ECMP to distribute traffic across the available nodes. It assigns IP addresses to services and announces a route to these IPs from multiple nodes. More information available [here](http://docs.romana.io/Content/advanced.html#layer-3-load-balancing).

