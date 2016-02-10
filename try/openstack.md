---
layout: page
title: Try Romana on OpenStack Now
menu_text: OpenStack
nav_text: Try it Now
icon: launch
firstnav: 2
secondnav: 1
permalink: /try_romana/openstack/
---

Romana v0.6 lets you build isolated multi-tenant networks in OpenStack without an overlay network. The current installer builds an OpenStack cluster in AWS on EC2 instances or locally on your laptop in Virtualbox VMs configured with Vagrant.

To run in AWS, you will need to set up your laptop with the AWS command line tools and have your own AWS account to launch the script that provision the EC2 instances as OpenStack nodes and installs Romana. The Romana repository's [README]( https://github.com/romana/romana/) file has the latest detail on how to set up your environment and get started. 

Local Virtualbox installation requires Ubuntu 14.04 LTS, but should work similarly on other Linux or Mac OS X environments.

#### What Gets Installed

The EC2 installation takes about 20-25 mins to complete (on t2.large instances) and creates an OpenStack DevStack cluster (Liberty release) with a single Controller Node and up to 4 Compute Nodes. Each OpenStack Node runs on a dedicated EC2 instance. Romana installs its OpenStack ML2 and IPAM drivers and creates a Romana router gateway interface on each Compute Node. 

The default configuration partitions the address space so that 8 bits are used for the Host ID, 4 bits for the Tenant and Segment IDs as well as 8 bits for the endpoint ID. While this may not be an efficient allocation for a production deployment, it has the advantage of creating IP addresses that make it easy to see what is happening in the example. 

>  This address bit allocation is also different from the example used to illustrate how Romana uses IP addresses for [tenant isolation](/how/romana_details/#romana-tenant-isolation)

Two OpenStack tenants (projects) are created: 'admin' and 'demo'. They each have up to 2 network segments named 's1' and 's2'. The segment is specified by setting a value for the 'Romana Network Segment' field in the 'Advanced Options' tab in Horizon when an Instance is launched. 


#### What You Can Do

Once you have successfully installed OpenStack, you can explore the setup by examining the route tables on the Compute Nodes. You can log in to Horizon at the IP address of the Controller Node. The default administrator account is 'admin' with password 'secrete'. 

You can ssh in to the EC2 instance using it's public IP address. There you will find the local Romana router gateway interface (romana-gw) that is used to access endpoints running on the local host, as well as routes to the *other hosts* that lie on the 192.168.99.0/24 network (eth0). 

The route table should looks something like the configuration below:

	ubuntu@ip-192-168-99-10:~$ route -n
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
	0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 eth0
	10.0.0.0        0.0.0.0         255.255.0.0     U     0      0        0 romana-gw
	10.1.0.0        192.168.99.11   255.255.0.0     UG    0      0        0 eth0
	192.168.99.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
	
Note that since there are 8 bits allocated for the Host ID, the gateway interfaces will be to /16 networks (32-8-8=16). 

From Horizon, examining the 'admin' project you will see an empty network. You can start several cirros VMs and put them all on their own segment by specifying a segment name in the launch instance 'Advanced Options' tab.

Launch three or four instances on each network segment. Once they have started, you can examine the route table on the Compute Nodes where you will see tap interfaces for each VM running on the node. Note the way the addresses have been set using Host, Tenant and Segment IDs as shown in the example below.

	ubuntu@ip-192-168-99-10:~$ route -n
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
	0.0.0.0         192.168.99.1    0.0.0.0         UG    0      0        0 eth0
	10.0.0.0        0.0.0.0         255.255.0.0     U     0      0        0 romana-gw
	10.0.18.5       0.0.0.0         255.255.255.255 UH    0      0        0 tapa2ce27af-ba
	10.0.18.6       0.0.0.0         255.255.255.255 UH    0      0        0 tap0ac95cd0-70
	10.0.19.5       0.0.0.0         255.255.255.255 UH    0      0        0 tap72d6054e-51
	10.1.0.0        192.168.99.11   255.255.0.0     UG    0      0        0 eth0
	192.168.99.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
	
If you log in to any of the instances (from Compute Node, 'ssh cirros@IP_address', or use the Console tab of the Horizon instance detail), you will be able to ping other instances on the same segment. If you *traceroute* the path to another instance on the *same* host, you will see the local Host IP address as a router hop along the path. 

	$ traceroute -n -w 1 10.0.18.6
	traceroute to 10.0.18.6 (10.0.18.6), 30 hops max, 46 byte packets
 	1  10.0.0.1   34.191 ms  0.038 ms  0.441 ms
 	2  10.0.18.6  196.423 ms  0.286 ms  1.154 ms

Note: A *traceroute* to an instance on a different host will show blank entries (* * *) for the router hops along the path. This is normal, since ICMP traffic is blocked by default across OpenStack Hosts.

		$ traceroute -n -w 1 10.1.18.7
	traceroute to 10.1.18.7 (10.1.18.7), 30 hops max, 46 byte packets
 	1  10.0.0.1   0.129 ms  0.035 ms  1.539 ms
 	2  *  *  *
 	3  10.1.18.7  1.242 ms  0.906 ms  1.499 ms

Trying to ping instances on different segments will fail as expected since they are isolated.

Try launching more instances as a different tenant (demo). There you will see addresses being assigned to the instances with a different Tenant ID. Pinging these instances from any of the 'admin' tenant instances will fail as expected since they are separate isolated tenants.

You can build a DevStack cluster with a different number of Compute Nodes by changing the *devstack_compute_node* value in the [configuration file](https://github.com/romana/romana/blob/master/romana-install/group_vars/all) and running ./romana-setup again.

Romana provides a basic command line utility list or add new Hosts, Tenants, Segments, etc.  If you are logged in to the OpenStack Controller, you can list the Romana Tenants and/or Hosts with the following commands:

	ubuntu@ip-192-168-0-10:~$ romana show-tenant
	ubuntu@ip-192-168-0-10:~$ romana show-host

You can visit the other Romana repositories to see the [Go code, run tests](https://github.com/romana/core), etc. and the [OpenStack ML2 and IPAM drivers](https://github.com/romana/networking-romana).
