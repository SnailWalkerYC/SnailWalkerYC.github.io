---
layout: post
title: Clock Synchronization
date: 2020-01-28
categories: [technology]
tags: [tech]
comments: false

---



- 问题来源

不同设备的时间同步是非常重要的，尤其是高频的嵌入式设备以及分布式系统里面。



在同步的时候，存在时钟偏差（clock skew）和时钟漂移（clock shift）的问题。



UTC时间 + leap seconds（当前是34s） = TAI，这个值会慢慢接近UT1（格林威治时间，和地球自转同步）



- 时钟

UTC（PC时间的来源）和GPS时间（以UTC为基准，按一周的秒数计算出时间）。



- 物理时钟同步

1. Cristian算法：client A在t1发送给server，server发送给自己的时间T给client A，然后client A收到的时间是t2，所以client A的时间这时候设置为 T + （t2-t1）* 0.5

2. 伯克利算法：所有的时钟把时间发给server，server计算平均值再发回去。

3. NTP算法：master发送T1给slave，slave在T2收到了信息。T3发送信息给master，master在T4收到了信息，发回给slave。所以slave可以计算出延时 delay = (T4 - T3 + T2 - T1) * 0.5；时钟偏移 offset = (T2 - T1 + T3 - T4) * 0.5

   

- 逻辑时钟

  根据事件发生的逻辑学进行事件排序。

  Lamport逻辑时钟和vector clock。



Ref：

> https://www.cs.bham.ac.uk/~bxb/courses/02_03_DS/Lecture10_Handouts.pdf 
>
> https://www.cs.cmu.edu/~dga/15-440/F11/lectures/09-time+synch.pdf
>
> https://blog.csdn.net/woswod/article/details/82345380
>
> https://queue.acm.org/detail.cfm?id=1773943