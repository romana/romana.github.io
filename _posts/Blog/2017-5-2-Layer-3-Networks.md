---
layout: post
title: Layer 3 Network Deployments 
categories:
- blog
permalink: /blog/layer-3-networks/
---

*This post is the third in a series that [introduces]( /blog/topology-aware-IPAM/) Romana's new topology aware IPAM (TA-IPAM) and layer 3 deployment options* 

Running Romana on layer 3 networks introduces the added complexity of configuring the network to know how to reach pods and VMs that get launched by cloud orchestration systems on hosts. As we learned in the previous [post](/blog/layer-2-networks), layer 2 networks learn about other endpoints via broadcast messages. With layer 3 networks, there may be other devices along the path to the endpoints and these devices need routes to be added explicitly. 

Automating route distribution is problem that was solved long ago and there are several popular open source alternatives available. Romana v1.5 support [exabg](https://github.com/Exa-Networks/exabgp/wiki). [Bird](http://bird.network.cz/) and [Quagga](http://www.nongnu.org/quagga/) to meet a variety of distinct deployment requirements.

For datacenter deployments running a fully routed network design, each host becomes its own [stub network[(https://en.wikipedia.org/wiki/Stub_network). This simple L3 configuration requires only that the host network announce the default upstream to the Top of Rack device. 

[Insert Diagram Here}

This simple form of route distribution is provided by simple BGP speakers that don't include many of the other features typically found in a router. Two popular BGP speakers are exaBGP and Bird. Romana v1.5 will install one of these alternatives on hosts that require route distribution.

For deployments in EC2 that span multiple regions, routing is also required. However, since EC2 instances are on layer 2 segments, several  routed network design alternatives are possible. The details of multi-region EC2 deployments are details in the next [post](/blog/multi-region-networks).

For more complicated datacenter deployments the might include dual homed hosts, L2 access networks, or require hybrid cloud networking, Romana v1.5 also supports installation and configuration of Free Range Router software on hosts as necessary.




