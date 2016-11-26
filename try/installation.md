---
layout: page
title: Try Romana on Now
menu_text: Installation
nav_text: Try it Now
icon: launch
firstnav: 2
secondnav: 1
permalink: /try_romana/installation/
---

#  Installation

To get up and running with Romana, some scripts and Ansible playbooks have been provided to automate the setup and deployment.
This can be used to set up a cluster for experimenting with Romana, exploring how it works and learning how it interacts with Kubernetes and/or Openstack.

The installer is currently capable of setting up a stand-alone [Kubernetes](/try_romana/kubernetes/) or OpenStack-Devstack cluster. As deployment targets for those clusters, it supports [Amazon EC2](https://aws.amazon.com/ec2/), local [Vagrant](https://www.vagrantup.com/) VMs, or user-provided ("static") hosts.

* [Romana on AWS EC2 with Kubernetes](https://github.com/romana/romana/blob/master/aws_kubernetes.md)
* [Romana on AWS EC2 with Devstack](https://github.com/romana/romana/blob/master/aws_devstack.md)
* [Romana on Vagrant VMs with Kubernetes](https://github.com/romana/romana/blob/master/vagrant_kubernetes.md)
* [Romana on Vagrant VMs with Devstack](https://github.com/romana/romana/blob/master/vagrant_devstack.md)
* [Romana on User-Provided Hosts](https://github.com/romana/romana/blob/master/static_hosts.md)

See the [`romana-setup` page](https://github.com/romana/romana/blob/master/romana_setup.md) for details about the installer and the full set of command-line options available.

