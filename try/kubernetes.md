---
layout: page
title: Multi-host Kubernetes with Romana 
menu_text: Kubernetes
nav_text: 
icon:
firstnav: 2
secondnav: 2
permalink: /try_romana/kubernetes/
---

Romana v0.6 lets you run Kubernetes without an overlay network. You can use Romana to deploy Kubernetes in EC2 or on VMs running on your laptop with Vagrant. In addition, Romana supports the new [Network Policy features](/blog/MeetupDemo/) of Kubernetes v1.2. 

If you want to run Kubernetes with a simple, transparent, high performance network, just install Romana and you'll be on your way. See the installation details in the Github repository [README]( https://github.com/romana/romana) to get started. Later, after you have everything working the way you want, you can add advanced Network Policies as without any disruption.
  
#### What Gets Installed

The installation takes about 15-20 mins to complete (on t2.large instances) and creates a Kubernetes cluster with a single Master Node and up to 4 Minions. Each Node runs on a dedicated VM (on your laptop when you use Vagrant or as an EC2 instance). Romana installs its IPAM and Route Controller services on the Kubernetes Master Node, as well as a local agent and the [CNI plugin](https://github.com/appc/cni) on the Minions.

#### Network Isolation

To support tenant isolation and more advanced network policies, the default installation partitions the address space so that 8 bits are used for the Host ID, 4 bits for the Tenant and Segment IDs as well as 8 bits for the Endpoint ID. While this may not be an efficient allocation for a production deployment, it has the advantage of creating IP addresses that make it easy to see what is happening in the example. 

>  This address bit allocation is also different from the example used to illustrate how Romana uses IP addresses for [tenant isolation](/how/romana_details/#romana-tenant-isolation)

Two Kubernetes tenants, or application 'owners' are created: 't1' and 't2'. They each have up to 2 network segments named 'frontend' and 'backend'. The tenant and segment are specified by setting a value for the 'owner' and 'tier' labels in the Pod specification as shown below. A command line tool is available to add new tenants and segments as well.


	apiVersion: v1
	 kind: Pod
	 metadata:
	  name: nginx-frontend
	  labels:
	    app: nginx
	    owner: t1
	    tier: frontend
	 spec:
	 containers:
	   - name: nginx
	    image: nginx
	    ports:
	   - containerPort: 80

	apiVersion: v1
	 kind: Pod
	 metadata:
	  name: nginx-frontend
	  labels:
	   app: nginx
	   owner: t1
	   tier: backend
	 spec:
	 containers:
	   - name: nginx
	   image: nginx
	  ports:
	  - containerPort: 80


>Note: The final form of Kubernetes Network Policies will allow *network isolation* to be applied to an entire namespace. When isolation is enabled for a namespace, the default policy will be to block all traffic in and out of Pods. A specific Network Policy needs to be applied to the Pod to indicate the allowed communications. The current release of Romana uses 'owner' labels but will be updated to use namespace isolation as soon as practical.

#### Kubernetes v1.2 Network Policy

Romana is installed as a Third Party resource for Kubernetes, which lets it watch for Pod and Network Policy changes and respond by configuring the network as necessary. By default, the network policy for Kubernetes is to behave just as it always has: All Pods have complete access to all other Pods. 

However, new network policies may also be configured by applying a NetworkPolicy resource as shown below:

	kind: NetworkPolicy
	apiVersion: romana.io/demo/v1
	metadata:
	 name: policy1
	 namespace: default
	 labels:
	 - owner: t1
	 spec:
	  podSelector:		// Standard label selector - selects pods.
	   tier: backend
	 allowIncoming:		// (Optional) List of allow rules.
	  - toPorts:		// (Optional) List of dest ports to open.
	  - port: 80		// (Optional) Numeric or named port 
	  protocol: TCP		// [ TCP | UDP]
	 from:			// (Optional) List of sources.
	  - pods:		// (Optional) Standard label selector.
	  tier: frontend	// (Optional) Standard label selector.

This policy will allow the backend Pods to accept TCP traffic on port 80 from the fontend Pods.

#### What You Can Do

Installed on each node is a demo script that sets up Pods with different tenant owners and confirms traffic isolation between the Pods using network policies like the one shown above. The [Using Romana on Kubernetes page](https://github.com/romana/romana/blob/master/kubernetes_romana.md) on GitHub has all the details. 


