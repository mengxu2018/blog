---
title: microservice service registry
date: 2023-08-10 15:58:30
tags: microservice
---

### service to service call

在微服务架构中，实际的微服务调用流量不会经过注册中心。注册中心主要负责维护微服务的元数据信息，如服务名称、实例地址和端口等，以便其他微服务可以发现和定位目标服务的实例。一旦微服务获得了目标服务的实例信息，后续的实际数据传输将直接在调用方和被调用方之间进行，绕过了注册中心。

这种直接的点对点通信方式有助于降低了注册中心的负担，并提高了系统的性能和效率。

实际的微服务调用流程如下：

1. 调用方的微服务通过服务注册中心获取目标微服务的实例信息。
2. 调用方的微服务直接与目标微服务的实例建立连接，进行实际的数据传输和调用。
3. 数据流从调用方直接传输到目标微服务，没有经过注册中心。

这种流程保证了微服务之间的实际数据传输是高效且直接的，同时也避免了在注册中心上产生不必要的流量。
