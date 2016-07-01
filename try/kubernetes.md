---
layout: page
title: Multi-tenant Kubernetes Networks with Romana 
menu_text: Kubernetes
nav_text: 
icon:
firstnav: 2
secondnav: 2
permalink: /try_romana/kubernetes/
---

Romana v0.9 lets you run Kubernetes without an overlay network. You can use Romana to deploy Kubernetes in EC2 or on VMs running on your laptop with Vagrant as well as on stand alone machines. In addition, Romana supports the new [Network Policy API](/blog/MeetupDemo/) included in Kubernetes v1.3. 

If you want to run Kubernetes with a simple, transparent, high performance network, just install Romana and you'll be on your way. See the installation details in the Github repository [README]( https://github.com/romana/romana) to get started. Later, after you have everything working the way you want, you can add advanced Network Policies as without any disruption.
  
#### What Gets Installed

The installation takes about 15-20 mins to complete (on t2.large instances) and creates a Kubernetes cluster with a single Master Node and up to 4 Minions. Each Node runs on a dedicated VM (on your laptop when you use Vagrant or as an EC2 instance). Romana installs its IPAM and Route Controller services on the Kubernetes Master Node, as well as a local agent and the [CNI plugin](https://github.com/appc/cni) on the Minions.

#### Network Isolation

To support tenant isolation and more advanced network policies, the default installation partitions the address space so that 8 bits are used for the Host ID, 4 bits for the Tenant and Segment IDs as well as 8 bits for the Endpoint ID. While this may not be an efficient allocation for a production deployment, it has the advantage of creating IP addresses that make it easy to see what is happening in the example. 

>  This address bit allocation is also different from the example used to illustrate how Romana uses IP addresses for [tenant isolation](/how/romana_details/#romana-tenant-isolation)

#### Kubernetes v1.2 Network Policy

Romana is installed as a Third Party resource for Kubernetes, which lets it watch for Pod and Network Policy changes and respond by configuring the network as necessary. By default, the network policy for Kubernetes is to behave just as it always has: All Pods have complete access to all other Pods. 

For more granular control of traffic to Pods, you can isolate them, then apply a more specific Network Policy.  Network isolation is enabled on a per namespaces basis allowing Kubernetes operators to create isolated namespaces for use by individual project tenants.

The Romana installation creates two Kubernetes namespaces: 'tenant-a' and 'tenant-b' and enables network isolation for each by setting the 'network-isolation' annotation to 'on' as shown below.

	apiVersion: v1
	kind: Namespace
	metadata:
	 name: "tenant-a"
	 labels:
	  name: "tenant-a"
	 annotations:
	  net.alpha.kubernetes.io/network-isolation: "on"

When network isolation is enabled, all Pods are isolated and all communication is disabled. A specific network policy then needs to be applied to Pods to enable communications. Any label selector can be used to identify the Pods that get a network policy. The example below shows a NetworkPolicy named 'pol1' allowing incoming TCP traffic on port 80 to Pods with a 'segment' label of 'backend' to accept traffic from Pods labeled 'frontend'

	"metadata": {
	 "name" : "pol1"
	},
	"apiVersion" : "romana.io/demo/v1",
	"kind" : "NetworkPolicy",
	"spec" : { 
	 "podSelector" : { 
	  "segment" : "backend" 
	 },
	"allowIncoming" : {
	 "toPorts" : [
	  {
	   "port": 80,
	   "protocol": "TCP"
	  }
	 ],
	 "from" : [
	  {
	    "pods" : {
	   "segment" : "frontend"
	  }
	 }
	 ]
	}
}


#### What You Can Do

Installed on each node is a demo script that sets up Pods in different namespaces (tenants) with different segment label values.

	apiVersion: v1
	kind: Pod
	metadata:
	 name: nginx-backend
	 namespace: tenant-a
	 labels:
	  app: nginx
	  segment: backend
	spec:
  	 containers:
	 - name: nginx
	image: nginx
	 ports:
	 - containerPort: 80

You can step through the script to see the policy being applied and traffic being enabled between the Pods. The [Using Romana on Kubernetes page](https://github.com/romana/romana/blob/master/kubernetes_romana.md) on GitHub has all the details. 


