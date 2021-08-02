---
layout: post
title: Backend Tool Summary
date: 2019-08-01
categories: [technology]
tags: [life]
comments: false
---



# RPC

RPC（Remote Procedure Call）远程过程调用，简言之就是像调用本地方法一样调用远程服务。目前外界使用较多的有gRPC、Dubbo、Spring Cloud等。



**Basic:**

- 服务提供方 Serivce Provider:其需要对外提供服务接口，它需要在应用启动时连接注册中心，将服务名及其服务元数据发往注册中心。同时需要提供服务服务下线的机制。需要维护服务名和真正服务地址映射。服务端还需要启动Socket服务监听客户端请求。
- 服务消费方 Servce Consumer: 客户端需要有从注册中心获取服务的基本能力，它需要在应用启动时，扫描依赖的RPC服务，并为其生成代理调用对象，同时从注册中心拉取服务元数据存入本地缓存，然后发起监听各服务的变动做到及时更新缓存。在发起服务调用时，通过代理调用对象，从本地缓存中获取服务地址列表，然后选择一种负载均衡策略筛选出一个目标地址发起调用。调用时会对请求数据进行序列化，并采用一种约定的通信协议进行socket通信。
- 注册中心 Registery: 要是用来完成服务注册和发现的工作。虽然服务调用是服务消费方直接发向服务提供方的，但是现在服务都是集群部署，服务的提供者数量也是动态变化的，所以服务的地址也就无法预先确定。因此如何发现这些服务就需要一个统一注册中心来承载。

目前成熟的注册中心有Zookeeper，Nacos，Consul，Eureka;  **IO通信框架**: 本实现采用Netty作为底层通信框架，Netty是一个高性能事件驱动型的非阻塞的IO(NIO)框架。

**通信协议**: TCP通信过程中会根据TCP缓冲区的实际情况进行包的划分，所以在业务上认为一个完整的包可能会被TCP拆分成多个包进行发送，也有可能把多个小的包封装成一个大的数据包发送，这就是所谓的TCP粘包和拆包问题。所以需要对发送的数据包封装到一种通信协议里。

业界的主流协议的解决方案可以归纳如下：

1. 消息定长，例如每个报文的大小为固定长度100字节，如果不够用空格补足。
2. 在包尾特殊结束符进行分割。
3. 将消息分为消息头和消息体，消息头中包含表示消息总长度（或者消息体长度）的字段。



**序列化协议**

本实现支持3种序列化协议，JavaSerializer、Protobuf及Hessian可以根据配置灵活选择。建议选用Protobuf，其序列化后码流小性能高，非常适合RPC调用，Google自家的gRPC也是用其作为通信协议。



**负载均衡**

本实现支持两种主要负载均衡策略，随机和轮询，其中他们都支持带权重的随机和轮询，其实也就是四种策略。

[Ref](https://zhuanlan.zhihu.com/p/388848964)



# Database

See [here](http://yuchenspace.info/Database-Summary/) & [LevelDB](https://www.zhihu.com/topic/19819000/hot)





# Workflow

Argo Workflows is an open source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition).



- Define workflows where each step in the workflow is a container.
- Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
- Run CI/CD pipelines natively on Kubernetes without configuring complex software development products.
- Retry mechanisms to increase reliability





