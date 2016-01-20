---
layout: page
title: Romana Performance
menu_text: Performance
nav_text: NA
firstnav: 2
secondnav: 5
permalink: /how/performance/
---

Since Romana avoids the use of virtual network overlays and [VXLAN encapsulation](/how/background/#vxlan-tenant-isolation), significant performance gains can be achieved.

While VXLAN technology is reasonably mature and stable, bandwidth and CPU performance overhead are often cited as [areas for concern]( http://www.enterprisenetworkingplanet.com/netsp/vxlan-beyond-the-hype.html).

This is because the overlay requires a Virtual Tunnel Endpoint (VTEP) processor to add a header and to insert the correct field value for each packet that enters or exits the VXLAN. While line rate throughput can be achieved, the actual observed performance impact is [difficult to characterize](http://blog.ipspace.net/2015/02/performance-of-hypervisor-based-overlay.html). However, with header overhead [reducing bandwidth by ~6%](http://packetpushers.net/vxlan-udp-ip-ethernet-bandwidth-overheads/), and [CPU overhead taking at least 3%](http://chinog.org/wp-content/uploads/2015/05/Optimizing-Your-Virtual-Switch-for-VXLAN.pdf) the overall performance impact cannot be ignored. The reduced MTU size, for example may introduce packet fragmentation that could significantly impact [performance](http://www.networkworld.com/article/2224654/cisco-subnet/mtu-size-issues.html). 
 
But more important to consider than simple stand alone performance benchmarks is the cumulative latency introduced by encap/decap cycles along the complete path. At a minimum, all east/west traffic requires an encap at the source and a decap at the destination, or one complete cycle. However, for all routed VXLAN and north/south traffic, there is an *additional* encap/decap cycle for *each* intermediate router and/or Service Function. This is especially problematic for Cloud Native (i.e. microservice oriented) applications since they frequently apply Service Functions (i.e. load balancers) between *each* microservice.

In addition, since overlay networks remove all topology context from the network, inefficient packet paths are unavoidable. It are these inefficient packet paths that most significantly impact performance.

### Performance Gains on OpenStack Networks


The diagram below shows the path packets take when OpenStack VMs on different tenant VXLAN segments communicate. There is an extra encap/dcap cycle required for the router on the Neutron host to forward traffic to the proper endpoint. 

![OpenStack VXLAN Routing]({{ site.baseurl }}/images/vxlan.png) 

Using Roman not only eliminates the VXLAN encapsulation, but also enables direct packet forwarding to the destination host, resulting in signiciant performance gains. The diagram below shows the path packets take when routed directly to their destination Hosts.

![Romana Routing]({{ site.baseurl }}/images/directrouting.png) 

The table below shows the latency improvements seen on OpenStack traffic for Romana vs. standard VXLAN overlays. For routed VXLAN traffic where the destination is on the same host (i.e. local) latency drops from 1.5ms to 0.24 ms, or about 85% reduction in latency. This is because with Romana, all traffic remains local, whereas, the routed VXLAN traffic needs to be forwarded of host to the Neutron router, then back. 

Traffic destined for a different host shows (remote traffic) about a 50% reduction in latency.


| Time | North/South | North/South | East/West | East/West |
| | Local | Remote | Local | Remote |
| Native OpenStack | 1.51 ms | 1.51 ms | 0.24 ms | 0.85 ms |
| Romana Networks | 0.24 ms | 0.77 ms | 0.24 ms | 0.77 ms |
| *Relative Performance* | Local | Remote | Local | Remote |
| Native OpenStack | 100% | 100% | 100% | 100% |
| Romana Networks | 16% | 51% | 100% | 90% |
{: class='romanatable'}

For traffic that remains on the same network segment (i.e. no VXLAN routing), Romana demonstrates the same performance for local destinations and improved performance for remote destinations. 

This is because for local destinations both Romana and open vSwitch use the linux kernel to forward packets. For remote destinations, Romana does deliver improved performance (~10%) since no encapsulation is necessary.



