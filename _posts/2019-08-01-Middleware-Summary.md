---
layout: post
title: Middleware Summary
date: 2019-08-01
categories: [technology]
tags: [life]
comments: false
---



# Web Server

**Usage：**receive & process request, load balance problem. Listen to http request with some opening port. Maybe 1k request concurrent, but 10k for each second.

**Framework:** Nginx, OpenResty, Tomcat, Django



# API Gateway

**Usgae:** An API gateway takes all API calls from clients, then routes them to the appropriate microservice with request routing, composition, and protocol translation. Typically it handles a request by invoking multiple microservices and aggregating the results, to determine the best path. It can translate between web protocols and web‑unfriendly protocols that are used internally. 

**Framework:**  Tyk, Kong, Zuul, Orange.



# Cache 

**Usage:** **distributed cache** is a system that pools together the random-access memory (RAM) of multiple networked computers into a single in-memory data store used as a data cache to provide fast access to data. While most caches are traditionally in one physical server or hardware component, a distributed cache can grow beyond the memory limits of a single computer by linking together multiple computers–referred to as a [distributed architecture](https://hazelcast.com/glossary/distributed-computing/) or a distributed cluster–for larger capacity and increased processing power. 

Application acceleration; Storing web session data; Decreasing network usage/costs; Reducing the impact of interruptions; Extreme scaling. [here](https://hazelcast.com/glossary/distributed-cache/) 

**Framework:**  Redis, Memcached ...



# RPC Framework

**Usage:** Remote procedure call, RPC. A **remote procedure call** (**RPC**) is when a computer program causes a procedure ([subroutine](https://en.wikipedia.org/wiki/Subroutine#:~:text=In_computer_programming,_a_subroutine,particular_task_should_be_performed.)) to execute in a different [address space](https://en.wikipedia.org/wiki/Address_space) (commonly on another computer on a shared network), which is coded as if it were a normal (local) procedure call, without the programmer explicitly coding the details for the remote interaction.

**Framework:** Tars, Dubbo, gRPC, Thrift ...



# Database Sharding

**Usage:** **Database sharding** is the process of making partitions of data in a database or search engine, such that the data is divided into various smaller distinct chunks, or **shards**.

**Framework:** Sharding jdbc.



# Logs

**Usage:** fetch logs & analyze the logs. ELK B. 

**Framework:** Elasticsearch, Logstash, Kibana, Beats.



# Configuere Server

**Usage:** Store all configures & update.

**Framework:** Apollo, Zookeeper.



All service in one -> 随着需求增大，很难维护。当里面有个业务模块负载过高时，并不能够单独扩展该服务，必须扩展整个应用程序（就是这么霸道），这可能导致额外的资源浪费。

Micro-service：微服务是由以单一应用程序构成的小服务，自己拥有自己的行程与轻量化处理，服务依业务功能设计，以全自动的方式部署，与其他服务使用 HTTP API 通信。同时服务会使用最小的规模的集中管理 (例如 Docker) 能力，服务可以用不同的编程语言与数据库等组件实现 。

Advantage：技术异构性，可以使用不同的micro-service；isolation；scalable；easy deploy；easy to optimization。

Disadvantage: 服务注册和发现；服务容错；服务监控；服务安全。



# Virtualization

虚拟化，是指通过虚拟化技术将一台计算机虚拟为多台逻辑计算机。



OpenStack 管理 VM（Virtual Machine）虚拟机（KVM，Xen，OpenVZ）的工具；Kubernetes 简称 K8s ，是管理 container 容器（Docker）的工具。





Ref: [here](https://www.zhihu.com/question/24952874)

[Micro-Service](https://mp.weixin.qq.com/s/avX_QOftWNHNfY3bqBzS9Q)

