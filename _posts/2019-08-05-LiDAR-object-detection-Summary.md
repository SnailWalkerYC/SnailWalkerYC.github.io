---
layout: post
title: LiDAR object detection Summary
date: 2019-08-05
categories: [technology]
tags: [coding]
comments: false
---



### Point Pillar

Bird-eye view.



### Laser Net

Input height, intensity & depth & image. Project the LiDAR point to the image. Then do the 2D image detection.



### Multi-fusion

bird-eye view & perspective voxel view(polar corrdinate). 



### Range RCNN

提取range feature，再映射回bird view。



### NMS && Anchors



### AFDet 

NMS & Anchor free.



### Lasso & Ridge Regression

Lasso could reduce dimension.

https://towardsdatascience.com/ridge-and-lasso-regression-a-complete-guide-with-python-scikit-learn-e20e34bcbf0b