---
layout: post
title: Run Kubernetes Without an Overlay Network
categories:
- blog
permalink: /blog/kubernetes/
---

As part of getting ready for [KubeCon](/blog/KubeCon/) next week we've been showing Kubernetes with Romana and the new Network Policy features at [Meetups](http://www.meetup.com/Bay-Area-Kubernetes-Meetup/events/228213245/) around the [Bay Area](http://www.meetup.com/openvswitch/events/229046426/), and now everyone can try it out.

Today we released an updated [version](/try_romana/kubernetes/) that includes automated installation and configuration of Kubernetes.

You can run this installer to deploy Kubernetes on EC2 or your laptop. From there you can use Kubernetes just as you would with any other installation. However, with Romana installed you won't have the overhead of a virtual network overlay so your Nodes will communicate more directly without tunnels or encapsulation.

In addition, with Romana you'll be able to try out some of the new Network Policy examples that show isolation across Pods. 

We'd love to see some real world examples of Kubernetes apps running with Romana to see the performance gains that come with Cloud Native networks. So if you have any examples, please let us know.

Finally, we've made it super easy to launch some Pods with network isolation using the new network policy features with a demo script that creates Pods and applies a network policy, just like we've shown at the Meetups. 

If you want to see it all in action, this [recording](https://youtu.be/qSeVvVn3K2U?t=24m30s) of the demo from the Meetup last week is a good place to start.

![](https://i.ytimg.com/vi_webp/qSeVvVn3K2U/mqdefault.webp)

