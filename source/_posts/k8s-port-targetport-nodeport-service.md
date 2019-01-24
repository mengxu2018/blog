---
title: k8s service port
---

*总的来说，port和nodePort都是service的端口，前者暴露给集群内客户访问服务，后者暴露给集群外客户访问服务。从这两个端口到来的数据都需要经过反向代理kube-proxy流入后端pod的targetPort，从而到达pod上的容器内。*

*nodeport只有 type为NodePort类型的的service用*

容易混淆的概念：
```aidl
1、NodePort和port

前者是将服务暴露给外部用户使用并在node上、后者则是为内部组件相互通信提供服务的，他们是在service上的端口。

2、targetPort
targetPort是pod上的端口，用来将pod内的container与外部进行通信的端口

3、port、NodePort、ContainerPort和targetPort在哪儿？

port在service上，负责处理对内的通信，clusterIP:port

NodePort在node上，负责对外通信，NodeIP:NodePort

ContainerPort在容器上，用于被pod绑定

targetPort在pod上、负责与kube-proxy代理的port和Nodeport数据进行通信
```

```
  apiVersion: v1
        kind: Service
        metadata:
          name: test-service
        spec:
          ports:
          - port: 8080
            targetPort: 8170
            nodePort: 33333
            protocol: TCP 
          selector:
            component: test-service-app
```
The port is 8080 which represents that test-service can be accessed by other services in the cluster at port 8080. 
The targetPort is 8170 which represents the test-service is actually running on port 8170 on pods 
The nodePort is 33333 which represents that test-service can be accessed via kube-proxy on port 33333.
**pod运行在targetPort为8170不代表就可以通过这个端口访问，还是得通过port的8080来访问他(cluster内部)，或者nodePort的33333来访问（外部）**

## 个人总结
*nodeport是外部机器访问的端口，这个是刚刚到kube-proxy的时候，还没有进入具体的pod*
*targetport是具体的port的监听端口，比如mysql的3306，就是dockerfile里面的expose端口*
*port是内部pod直接的访问端口，这个端口也是刚刚到kube-proxy的时候，还没有进入具体的pod，这个跟nodeport一个级别，
比如mysql的targetport虽然是3306，但是cluster里面的pod可能通过33306来访问mysql pod*

*Kubernetes 分配给 Service 一个固定 IP，这是一个虚拟 IP （也称为 ClusterIP），并不是一个真实存在的IP,而是由 Kubernetes 虚拟出来的。虚拟 IP 的范围通过 Kubernetes API Service 的启动参数--service-cluster-ip-range=10.254.0.0/16 配置*
*虚拟 IP 属于 Kubernetes 内部的虚拟网络，外部是寻址不到的，在 Kubernetes 系统中，实际上是由 Kubernetes Proxy 组件负责实现虚拟 IP 路由和转发的，所以在 Kubernetes Node 中我们都运行了 Kubernetes Proxy，从而在容器覆盖网络之上又实现了 Kubernetes 层级的虚拟转发网络。*

 

*https://blog.csdn.net/xinghun_4/article/details/50492041*
*https://hk.saowen.com/a/e185eef3635890d22291e800f123eb2b1339d5e835017ab670c88832078ea291*
*https://ieevee.com/tech/2017/01/20/k8s-service.html*
*https://stackoverflow.com/questions/49981601/difference-between-targetport-and-port-in-kubernetes-service-definition*
*https://wiki.shileizcc.com/confluence/pages/viewpage.action?pageId=2523170*
*https://juejin.im/post/5bd6c14be51d455b7461cb46*
