---
title: kubernetes cluster setup
date: 2018-12-24 22:51:52
tags:
---
![kubernetes architecture](/images/Kubernetes.png)

## 准备机器
* 可以通过vmware创建1台centos虚拟机，我用的是7.6版本，可以从官网下载[centos](https://www.centos.org/download/)
* 安装完成之后会发现网卡是禁用的，通过nmtui命令开启网卡,然后yum install -y net-tools安装网络工具
* 需要提醒的是,下面所有的操作都是root用户

## 禁用防火墙
```
systemctl disable firewalld

```

## 安装 kubeadm, kubelet, kubectl, docker
* 参考[docker install](https://docs.docker.com/install/linux/docker-ce/centos/)来安装最新版的docker
* 设置kubernetes repository
```
  cat <<EOF > /etc/yum.repos.d/kubernetes.repo
  [kubernetes]
  name=Kubernetes
  baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
  exclude=kube*
  EOF
```

* 安装k8s相关工具
```
yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

* Set SELinux in permissive mode (effectively disabling it)
```
  setenforce 0
  sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

* 开机自启动kubernetes, docker
```
systemctl enable kubelet
systemctl enable docker
systemctl restart kubelet
systemctl restart docker
```

* 关闭swap
```
swapoff -a
vi /etc/fstab
注释掉下面这一行永久关闭swap
#/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

# clone这个vm生成两个新的work node

# 设置主机名

```
hostnamectl set-hostname 'k8smaster'
hostnamectl set-hostname 'node1'
hostnamectl set-hostname 'node2'
```

# Initializing your master

* 在master机器执行kubeadm init
```
kubeadm init --pod-network-cidr=10.244.0.0/16
```

* master上面更新/root/.bash_profile
**增加下面一行，source /root/.bash_profile 立即生效**
```
export KUBECONFIG=/etc/kubernetes/admin.conf

```

* 在master上 Installing a pod network add-on
```
sysctl net.bridge.bridge-nf-call-iptables=1
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```
Once a pod network has been installed, you can confirm that it is working by checking that the CoreDNS pod is Running in the output of 
**kubectl get pods --all-namespaces**

* work node上面更新/root/.bash_profile
**增加下面一行，source /root/.bash_profile 立即生效**
```
export KUBECONFIG=/etc/kubernetes/kubelet.conf

```

* 加node到cluster
 刚刚kubeadm init执行后输出的join命令可以在另外两台work node上面跑，跑完之后 Run **kubectl get nodes** on the master to see this machine join.

# master上配置dashboard

* 安装[dashboard](https://github.com/kubernetes/dashboard)
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

* create sample user
参考https://github.com/kubernetes/dashboard/wiki/Creating-sample-user

* 使用NodePort来配置
https://github.com/kubernetes/dashboard/wiki/Accessing-Dashboard---1.7.X-and-above#nodeport

* 得到dashboard端口
```
kubectl -n kube-system get service kubernetes-dashboard
```

* 执行下面命令得到dashboard跑在哪个node
```
kubectl get pods -n kube-system -owide
```

* 最后访问
```
https://<node-ip>:<nodePort>
```
不过因为没有安全证书，所以chrome和ie禁止访问，只能通过Firefox增加exception来访问，后续可以研究下更好的访问dashboard方法

# 相关命令
```
kubeadm reset
kubectl describe pods
journalctl -u kubelet.service
journalctl -xeu kubelet(查看日志)
kubectl get pods --all-namespaces
kubectl get pods -n kube-system -owide
kubectl logs -n kube-system kubernetes-dashboard-56bcddb89b-wbcqg
```