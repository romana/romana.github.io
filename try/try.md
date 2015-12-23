---
layout: page
title: Try Romana Now
menu_text: Try it Now
nav_text: Try it Now
icon: launch
firstnav: 3
secondnav: 9
permalink: /try_romana/
---

Romana v0.1 lets you build isolated multi-tenant networks in OpenStack on without an overlay network. The current installer builds an OpenStack cluster in AWS on EC2 instances. 

You will need to set up your laptop with AWS command line tools and have your own AWS account to launch the CloudFormation scripts that provision the EC2 instances as OpenStack nodes and installs Romana. The Romana repository's [README](http://www.github.com/romana/romana/blob/master/README.md) file has the latest detail on how to set up your environment and get started. 

We will be updating this release to also include a local deployment option using Vagrant.

#### What Gets Installed

The EC2 installation creates an OpenStack DevStack cluster (Liberty release) with a single Controller Node and up to 4 Compute Nodes, each running on their own EC2 instance. Romana installs its OpenStack ML2 and IPAM drivers and creates a Romana router gateway interface on each Compute Node. 

The default configuration partitions the address space so that 8 bits are used for the Host ID, 4 bits for the Tenant and Segment IDs as well as 8 bits for the endpoint ID. While this may not be an efficient allocation for a production deployment, it has the advantage of creating IP addresses that make it easy to see what is happening.

Two Tenants are created: 'admin' and 'demo'. They each have up to 2 network segments named 's1' and 's2'. The Segment is specified by setting a value for the 'Romana Network Segment' field in the 'Advanced Options' tab in Horizon when an Instance is launched. 


#### What You Can Do

Once you have successfully installed OpenStack, you can explore the setup by examining the route tables on the Compute Nodes. You can ssh in to the EC2 instance using it's public IP address. There you will find the local Romana router gateway interface (romana-gw) that is used to access endpoints running on this host, as well as routes to the *other hosts* that lie on the 192.168.0/24 network (eth0). 


Depending on how many Compute Nodes you're running, it should looks something like the configuration below:

    ubuntu@ip-192-168-0-10:~$ route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth0
    10.0.0.0        0.0.0.0         255.255.0.0     U     0      0        0 romana-gw
    10.1.0.0        192.168.0.11    255.255.0.0     UG    0      0        0 eth0
    10.2.0.0        192.168.0.12    255.255.0.0     UG    0      0        0 eth0
    10.3.0.0        192.168.0.13    255.255.0.0     UG    0      0        0 eth0
    10.4.0.0        192.168.0.14    255.255.0.0     UG    0      0        0 eth0
    192.168.0.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
    192.168.122.0   0.0.0.0         255.255.255.0   U     0      0        0 virbr0

Note that since there are 8 bits allocated for the Host ID, the gateway interfaces will be to /16 networks (32-8-8=16). 

From Horizon, examining the 'admin' project you will see an empty Network. You can start several cirros VMs and put them all on their own segment by specifying a Segment name in the launch Instance 'Advanced Options' tab.

Launch two or three instances on each network segments. Once they have started, you can examine the route table on the Compute Nodes where you will see tap interfaces for each VM running on the node.

If you log in to any of the instances (from Compute Node, 'ssh cirros@IP_address'), you will be able to ping other instances on the same segment. If you traceroute the path to other instances you will see two intermediate router hops along the path between the instances. Each host is a router hop, so two router entries in the trace indicate the router on the host at the source and the routed on the destination host.

Trying to ping instances on different Segments will fail.

You can relaunch DevStack with a different number of Compute nodes by changing the *devstack_compute_node* value in the [configuration file](https://github.com/romana/romana/blob/master/romana-install/group_vars/all) and running ./romana-setup again.

Beyond these simple tests, OpenStack will behave exactly as it always does.

> Release v0.1 does not support Tenant isolation

You can visit the other Romana [repositories](https://github.com/romana/core) to see the Go code, run tests, etc.
