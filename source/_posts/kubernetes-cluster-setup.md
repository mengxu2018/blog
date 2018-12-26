---
title: kubernetes cluster setup
date: 2018-12-24 22:51:52
tags:
---
![kubernetes architecture](/images/Kubernetes.png)

## 准备机器
* 可以通过vmware创建1台centos虚拟机，我用的是7.6版本，可以从官网下载[centos](https://www.centos.org/download/)
* 接下来所有的操作都是基于root用户，且需要配置科学上网，否则会遇到安装失败, 因为中间可能会访问到google地址

## 配置网卡
* 安装完成之后执行**nmcli d**会发现网卡是禁用的，ipconfig命令执行不了
* 关于网络配置可以参考[CentOS 7 minimal 版本安装后网络配置](https://www.cnblogs.com/hfyfpga/p/6262057.html)
 * 简单来说就是在nmtui打开的gui界面里面配置网卡自动启动
* 需要提醒的是nmtui打开的界面里面，通过tab切换按钮，通过空格而不是回车选择checkbox
* 网卡启动之后yum install -y net-tools安装网络工具
* ipconfig得到ip地址之后，强烈建议使用MobaXterm连接vm， 这个比putty好用很多，免费版就够用

## 禁用防火墙
*本地开发或者学习环境，为了简单，直接disable防火墙*
```
systemctl disable firewalld

```

## 安装 kubeadm, kubelet, kubectl, docker
* 参考[docker install](https://docs.docker.com/install/linux/docker-ce/centos/)来安装最新版的docker,或者直接执行下面三个命令
```
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo
      
yum install docker-ce
      
```
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

## 准备其余的机器

clone这个vm生成两个新的work node，分别设置三台机器各自的主机名
```
hostnamectl set-hostname 'k8smaster'
hostnamectl set-hostname 'node1'
hostnamectl set-hostname 'node2'
```

## Initializing your master
* Set /proc/sys/net/bridge/bridge-nf-call-iptables to 1
```$xslt
in master and all work nodes, execute:
sysctl net.bridge.bridge-nf-call-iptables=1
```
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

* 总结
 至此，执行kubectl get pods --all-namespaces可能会报错，可能是网络的问题， 通过journalctl -xeu kubelet查看相关日志，我是通过重启服务器来解决的
 
## master上配置dashboard

* 安装[dashboard](https://github.com/kubernetes/dashboard)
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```
安装完成之后，如果发现dashboard的pod不是running状态的话，可以删除他重新执行上面的安装命令
```$xslt
kubectl --namespace kube-system delete deployment kubernetes-dashboard
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

## 相关命令
```
kubeadm reset
kubectl describe pods
journalctl -u kubelet.service
journalctl -xeu kubelet(查看日志)
kubectl get pods --all-namespaces
kubectl get pods -n kube-system -owide
kubectl logs -n kube-system kubernetes-dashboard-56bcddb89b-wbcqg
```