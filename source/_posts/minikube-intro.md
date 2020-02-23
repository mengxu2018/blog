---
title: 使用minikube学习环境
date: 2020-01-27 20:44:15
tags: kubernetes
---

## minikube 
https://kubernetes.io/docs/setup/learning-environment/minikube/

安装好centos后因为之前设置静态ip的时候对vmware进行了设置，所以现在除了nmtui之外，也必须修改/etc/sysconfig/network-scripts/ifcfg-ens33

yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo
      
yum install -y docker-ce

systemctl disable firewalld

swapoff -a
vi /etc/fstab
注释掉下面这一行永久关闭swap
#/dev/mapper/centos-swap swap                    swap    defaults        0 0

sysctl net.bridge.bridge-nf-call-iptables=1


minikube start --vm-driver=none（not suggest,可能会有kubernetes版本不被istio支持的问题)
minikube start --memory=4096 --cpus=4 --kubernetes-version=v1.15 --vm-driver=none(--kubernetes-version可以不带，有时候带会有错误)
minikube start --memory=4096 --cpus=4 --vm-driver=none
minikube dashboard --url


下面是部署一个sample
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10
kubectl expose deployment hello-minikube --type=NodePort --port=8080
kubectl get pod
minikube service hello-minikube --url
kubectl describe services hello-minikube
```
[root@localhost ~]# kubectl describe services hello-minikube
Name:                     hello-minikube
Namespace:                default
Labels:                   app=hello-minikube
Annotations:              <none>
Selector:                 app=hello-minikube
Type:                     NodePort
IP:                       10.96.132.162
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31451/TCP
Endpoints:                172.17.0.4:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```
Endpoints:                172.17.0.4:8080是内网的
NodePort:                 <unset>  31451/TCP 是外网的

kubectl get namespace
kubectl get pods -n istio-system
kubectl describe pod  -n istio-system istio-galley-69674cb559-chxtg
kubectl logs -n istio-system istio-policy-5cdbc47674-q7flx


## istio gateway
kubectl get gateway
istioctl -n istio-system pc routes istio-ingressgateway-5db78457f5-h49tm --name http.80 -o json

```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
```
In the gateway resource, the selector refers to Istio’s default ingress controller by its label, in which the key of the label is istio and the value is ingressgateway
```
[root@localhost ~]# kubectl get pods -listio=ingressgateway -n istio-system
NAME                                    READY   STATUS    RESTARTS   AGE
istio-ingressgateway-649f9646d4-nc88s   1/1     Running   0          10h

感觉像是默认是要去和gateway resource同一个namespace下面去找gateway controller pod，找不到就找istio-system下面这个

https://stackoverflow.com/questions/51835752/how-to-create-custom-istio-ingress-gateway-controller
上面这个文章讲的比较详细，如果不是默认的istio: ingressgateway的话，需要gateway resource和gateway controller的namespace一致,
或者可以理解为gateway resource的默认namespace就是istio-system

https://discuss.istio.io/t/istio-ingressgateway-controller-and-namespaces/1217/5 的一个回答
In 1.1 this is changing a bit - the Gateway resource should be in same namespace as the gateway(the service, deployment, certificates). The default is istio-system - but you can run it in other namespace, or in multiple namespaces if you need to. Each namespace will have a different load balancer IP and may handle different domains with different certs.






https://blog.jayway.com/2018/10/22/understanding-istio-ingress-gateway-in-kubernetes/
```


但是对于官网下面的话不理解
One or more labels that indicate a specific set of pods/VMs on which this gateway configuration should be applied. The scope of label search is restricted to the configuration namespace in which the the resource is present. In other words, the Gateway resource must reside in the same namespace as the gateway workload instance.

最后这里总结下：
gateway resource和gateway controller pod是两个东西，前者可以通过kubeclt get gateway获得，后者其实就是一个pod，
我们平常一般创建gateway resource的时候默认没有指定namespace的时候是默认为isito-system，比如istio: ingressgateway，他会去
istio-system这个namespace下面去找label为istio: ingressgateway的gateway pod
如果要自己创建一个自定义的gateway controller pod的话，参考https://stackoverflow.com/questions/51835752/how-to-create-custom-istio-ingress-gateway-controller，简单说就是需要创建一个新的gateway pod资源，这个资源会在某个namespace下面，
然后在创建gateway resource并且指定他的namespace，同时指定selector匹配gateway pod资源

https://istio.io/blog/2019/custom-ingress-gateway/这个文章的第七点说明type: LoadBalancer可以创建一个gateway controller pod，
在上面的so文章也有人回答‘In fact, it is very simple. Istio's ingress is just a regular Kubernetes Service of "Load Balancer" type.’
但是有待验证


关于gateway，这个文章https://istio.io/docs/tasks/traffic-management/ingress/ingress-control/最好，
在minikube上面其实就是默认的ingress gateway通过nodeport暴露出来，所以访问具体的service其实是访问的这个gateway，
ingress说的其实就是这个gateway。
VirtualService里面的hosts应该说的重复字段，实际上destination里也有这个字段
https://blog.csdn.net/kozazyh/article/details/81477629这里就说明了这个重复

## troubleshooting

### network issue due to iptables
kubectl get pods -n istio-system
kubectl get pods -n kube-system
上面两个namespace下面的pod如果有下面的问题的话，就刷新iptables
none: coredns CrashLoopBackOff: dial tcp ip:443: connect: no route to host #4350

systemctl stop kubelet
systemctl stop docker
iptables --flush
iptables -tnat --flush
systemctl start kubelet
systemctl start docker

### multiply container due to the istio
When retrieving logs for pods that have multiple containers, you need to specify the container you want the logs for.
For example:
```
kubectl logs productpage-v1-84f77f8747-8zklx -c productpage
```
The reason is because Istio adds a second container to deployments via the istio-sidecar-injector. This is what adds the envoy proxy in front of all of your pods traffic.

### why kube-dns and core-dns both exist
kubectl get deployments -n kube-system
kubectl get pods -n kube-public
上面得到coredns
kubectl get svc -n kube-system
上面得到kube-dns
但是如果kubectl describe -n kube-system service/kube-dns就会发现
他的Selector: k8s-app=kube-dns其实就是指向kubectl get pods -lk8s-app=kube-dns -n kube-system



## reference
https://istio.io/docs/examples/bookinfo/
https://www.cnblogs.com/psy-code/p/9311104.html
kubectl get node -o wide
