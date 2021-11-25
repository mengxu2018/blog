---
title: microservice basics
date: 2021-11-25 22:51:52
tags: microservices
---


### service to service call
一般都是通过服务发现的方式找到对方service的ip，然后直接call，feign和spring cloud loadbalancer可以干这个事情，如果k8s那都不需要专门的服务发现框架，比如eureka，直接k8s的svc name就可以做到服务发现，负载均衡

### 微服务各组件选择
https://cloud.tencent.com/developer/article/1888723

注册中心：nacos，替代方案eureka、consul、zookeeper
配置中心: nacos ，替代方案spring cloud config、consul
调用:feign，替代方案：resttempate
熔断：sentinel、，替代方案：Resilience4j，Hystrix
熔断监控：sentinel dashboard
负载均衡:spring cloud loadbalancer
网关：spring cloud gateway
链路：spring cloud sleuth+zipkin，替代方案：skywalking等

nacos, sentinel都属于阿里巴巴的组件，如果使用阿里巴巴组件，也可以使用代替品

spring cloud的官网介绍了spring cloud alibaba，spring cloud Netflix，还有各个组件比如spring cloud config，其实spring cloud alibaba和spring cloud config不是一类东西，spring cloud alibaba提供了很多组件，但是也许并不全，还是可以结合使用spring cloud提供的单独的组件，比如spring cloud feign， spring cloud gateway