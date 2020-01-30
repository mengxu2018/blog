---
title: failed to start k8s
date: 2020-01-27 20:44:15
tags: kubernetes
---

可能是证书过期导致的，因为之前配置好的环境只要执行下面两个命令（在三台机器)
systemctl enable kubelet
systemctl enable docker

然后分别启动两个work node，然后过几分钟启动master node

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#manual-certificate-renewal