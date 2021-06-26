---
title: kubernetes basic 
date: 2021-03-06 22:51:52
tags: kubernetes
---
## ingress
In Kubernetes, an Ingress is an object that allows access to your Kubernetes services from outside the Kubernetes cluster. You configure access by creating a collection of rules that define which inbound connections reach which services.

https://stackoverflow.com/questions/56896490/what-exactly-kubernetes-services-are-and-how-they-are-different-from-deployments


## cluster context user
Cluster defines connection endpoint for Kubernetes API of a cluster.

User defines credentials for connecting to cluster.

Context defines both cluster and user.

https://stackoverflow.com/questions/56299440/kubectl-context-vs-cluster


## volumes
Volume decouples the storage from the Container. Its lifecycle is coupled to a pod. It enables safe container restarts and sharing data between containers in a pod.

Persistent Volume decouples the storage from the Pod. Its lifecycle is independent. It enables safe pod restarts and sharing data between pods.

https://stackoverflow.com/questions/51420621/what-is-the-difference-between-a-volume-and-persistent-volume


## ip

Each Pod has a single IP address assigned from the Pod CIDR range of its node. This IP address is shared by all containers running within the Pod, and connects them to other Pods running in the cluster.

Each Service has an IP address, called the ClusterIP, assigned from the cluster's VPC network. You can optionally customize the VPC network when you create the cluster.

Each Pod gets its own IP address, however in a Deployment, the set of Pods running in one moment in time could be different from the set of Pods running that application a moment later.

This leads to a problem: if some set of Pods (call them "backends") provides functionality to other Pods (call them "frontends") inside your cluster, how do the frontends find out and keep track of which IP address to connect to, so that the frontend can use the backend part of the workload? enter service.



## exec container or pod
https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/


## headless service

The working principle of a ClusterIP Service is as follows:

One Service may be backed by multiple endpoints (pods). A client accesses the cluster IP address and the request is forwarded to the real server based on the iptables rules to implement load balancing. For example, a Service is backed by two endpoints, but only the Service address is returned during DNS query. iptables determines the real server accessed by the client. However, when a headless Service is accessed, two actual endpoint records (pod IP addresses) are returned.

Therefore, the headless Service points directly to each endpoint, that is, each pod has a DNS domain name. In this way, pods can access each other, achieving inter-pod discovery and access.
