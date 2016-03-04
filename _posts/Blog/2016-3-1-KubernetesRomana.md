---
layout: post
title: Run Kubernetes Without an Overlay Network
categories:
- blog
permalink: /blog/kubernetes/
---

We've been [showing](http://www.meetup.com/Bay-Area-Kubernetes-Meetup/events/228213245/) Kubernetes and the upcoming Network Policy features around town [lately](http://www.meetup.com/openvswitch/events/229046426/), and now everyone can try it for themselves.

We've just release an updated version of [Romana](/try_romana/kubernetes/) that supports automated installation and configuration of Kubernetes. You can run this installer to deploy Kubernetes on EC2 or your laptop. From there you can try out some of the new Network Policy examples that show isolation across Pods. Or you can use Kubernetes however you want. But with the installation using Romana, there will be no overlay network so your Nodes will communicate  more directly without tunnels or encapsulations.  

We're really like to see some real world examples of Kubernetes apps running with Romana to see what kind of performance gains are seen. 

If you just want to see what it can do you can watch this demo from the Meetup last week[starting at 24:30](https://youtu.be/qSeVvVn3K2U?t=24m30s) 

![](https://i.ytimg.com/vi_webp/qSeVvVn3K2U/mqdefault.webp)

