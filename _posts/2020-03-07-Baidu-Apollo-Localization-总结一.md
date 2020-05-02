---
layout: post
title: Baidu Apollo Localization 总结一
date: 2020-03-07
categories: [technology]
tags: [book]
comments: false
---



这篇文章主要是对Baidu Apollo的论文*Robust and Precise Vehicle Localization based on Multi-sensor Fusion in Diverse City Scenes*的理解。



- 概述

  使用RTK-GPS、IMU、LiDAR融合进行定位。RTK-GPS会有信号被阻挡和汽车发出的微波信号由于多路径效应（在建筑比较密集，玻璃墙很多地方微波信号发生反射使得GPS收到的时间增长没法获得精确距离导致计算位置出现较大误差）；IMU会有累计漂移的问题；LiDAR对于雨雪天气，因为雨雪阻挡，使得激光束测距不准。

  这篇论文主要是提出了RTK-GPS、IMU、LiDAR后融合框架增强定位的可靠性，甚至可以给出不同传感器定位的时候的不确定性（uncertainty）；以及使用intensity map和elevation map进行（x, y, heading）的搜索。

- LiDAR定位

  使用LiDAR进行定位，会使用到事先生成的intensity map和elevation map，使用的是grid表示方式，每个grid使用一个高斯来同时维护intensity和elevation的均值方差。同时使用elevation和intensity进行匹配，并且有不同的权值，使得LiDAR定位更加稳定。