---
layout: post
title: Romana v0.8 Now Available
categories:
- blog
permalink: /blog/newrelease/
---

We're making a lot of great progress toward our first production release of Romana. Since we launched the project a few weeks ago we've gotten lots of great feedback and Romana is already part of a bunch of new deployment projects that are anxious for the first production ready release. 

Today we updated the repos to [version v0.8](https://github.com/romana/romana), which include a number of important new features.

* Kubernetes and OpenStack support
* Packaged installer for Kubernetes and DevStack on EC2 and local Vagrant clusters
* [CNI](https://github.com/appc/cni) Support
* Improved CLI
* Kubernetes Network Policy API extensions
* Support for both Ubuntu and CentOS clusters
* Improved IP address management

Lots of other improvement are under the hood in terms of your ability to install and configure Romana to suit your needs so please give it a try.

We're happy to say that both application developers and network operators are using Romana to simplify their networks. Kubernetes users have found that Romana can provide better isolation and security for their apps with the new [Network Policy APIs](/blog/MeetupDemo/).

Network operators have also realized that they can simplify their networks by removing overlay networks and VLANs from their deployments when their apps don't need them.

We're on track for a summer release of Romana v1.0 for production deployments. You can see our [roadmap](https://github.com/romana/romana/wiki/Roadmap/) for a list of the remaining features we've got to build in.

We're always available to answer your questions on our [Slack channel and mail lists](/code/), so if you're building Cloud Native applications, give it a try.





