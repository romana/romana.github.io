---
layout: page
title: Romana Performance
menu_text: Performance
nav_text: NA
firstnav: 1
secondnav: 4
permalink: /how/performance/
---

Since Romana avoids the use of virtual network overlays and [VXLAN encapsulation](/how/background/#vxlan-tenant-isolation), significant performance gains can be achieved. 

#### Improved Latency on OpenStack Networks

The diagram below shows the path packets take when OpenStack VMs on different tenant VXLAN segments communicate. There is an extra encap/dcap cycle required to reach the router on the Neutron host to forward traffic to the proper endpoint. 

![OpenStack VXLAN Routing]({{ site.baseurl }}/images/vxlan.png) 

Using Roman not only eliminates the VXLAN encapsulation, but also enables direct packet forwarding to the destination host, resulting in signiciant performance gains. The diagram below shows the path packets take when routed directly to their destination Hosts.

![Romana Routing]({{ site.baseurl }}/images/directrouting.png) 

The table below shows the latency improvements seen on OpenStack traffic for Romana networks vs. standard VXLAN overlays. For routed VXLAN traffic where the destination is on the same host (i.e. local) latency drops from 1.5ms to 0.24 ms, or about 85% reduction in latency. This is because with Romana, all traffic remains local, whereas, the routed VXLAN traffic needs to be forwarded off host to the Neutron router, then back. 

Traffic destined for a different host (remote traffic) shows about a 50% reduction in latency.


| Time | North/South | North/South | East/West | East/West |
| | Local | Remote | Local | Remote |
| Native OpenStack | 1.51 ms | 1.51 ms | 0.24 ms | 0.85 ms |
| Romana Networks | 0.24 ms | 0.77 ms | 0.24 ms | 0.77 ms |
| *Relative Performance* | Local | Remote | Local | Remote |
| Native OpenStack | 100% | 100% | 100% | 100% |
| Romana Networks | 16% | 51% | 100% | 90% |
{: class='romanatable'}

For traffic that remains on the same network segment (i.e. no VXLAN routing), Romana delivers the same performance as standard OpenStack for local destinations and improved performance for remote destinations. 

This is because for local destinations both Romana and Open vSwitch use the Linux kernel to forward packets. For remote destinations, Romana delivers improved performance of about 10% since no encapsulation is necessary.



