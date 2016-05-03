---
layout: post
title: Kubernetes on OpenStack Without an Overlay Network
categories:
- blog
permalink: /blog/OpenStackSummit/
---

We just got back from the OpenStack Summit in Austin where there were over 7,000 attendees. It seemed that container deployments and the new application architectures enabled by Kubernetes and other orchestration systems were part of many sessions, including ours.

Chris and Robert spoke about the new OpenStack IPAM API and their experiences developing the plug in for Romana. They were also able to demonstrate how IPAM is able to simplify OpenStack networking by eliminating overlay networks and use static routing for transparency and higher performance.  These are things we've talked about at [length](/how/romana_basics/). 

What got a lot of people's interest at the session was the ability of Romana to offer IPAM to Kubernetes running on OpenStack so that it also could run without an overlay network. 

Since Kubernetes uses multiple IP addresses per VM, it requires its own pod network. This is often deployed as an overlay using flannel, or one of a number of overlay network alternatives. When OpenStack builds it's own tenant networks, it also builds an overlay network.

Together, these two overlay networks impact performance dramatically and at the session Robert demonstrated Kubernetes running on OpenStack without any overlay at all, delivering the full performance of the native network.

You can watch the recording [here](https://www.youtube.com/watch?v=GlYbHlSVd1A). [Slides](http://www.slideshare.net/RomanaProject/openstack-summit-pliggable-ipam)




