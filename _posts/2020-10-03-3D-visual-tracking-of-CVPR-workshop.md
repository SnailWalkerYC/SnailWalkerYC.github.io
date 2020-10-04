---
layout: post
title: 3D visual tracking of CVPR workshop
date: 2020-10-03
categories: [technology]
tags: [life]
comments: false
---



3D multiple objects visual tracking（3D MOT）使用的是2D的视频序列进行多目标物体的3D追踪，就是在时间上需要对同一个物体进行关联匹配（association）以及计算出其位置，方向，速度的变化。



这个workshop比较好的是对于3D视觉多目标追踪新挖了一个大坑， 因为视觉追踪这块，2D的视觉MOT相对成熟一些，效果还可以用。但是3D的视觉追踪，和Lidar的比较起来还有很远的路要走~ 所以这种大坑的话发paper刷指标是容易一些，但是工业界实用性还是差一些（特指需要较高精度以及稳定性的自动驾驶领域）。



标准的3D MOT是由传感器数据，3D目标检测，时间上的关联，最后对追踪的结果进行评估。

- 传感器数据的话，做3D追踪可以由LiDAR、camera、radar等等传感器进行融合或者使用其中一种传感器进行。现在开源的数据有KITTI、NuScenes、Argo等等。
- 3D目标检测，可以由LiDAR（point pillar）、camera（image 2D detection）进行获取或者传感器的数据融合。3D检测如果是单目的话，根据workshop上面说的，到2020-01 AP才到30%，然后LiDAR的已经是接近85%了。当然，如果是sensor fusion的3D detection，应该是会更高。
- 检测结果的关联匹配，根据时间关系，位置变化，检测特征等等对于时间上前后的同一个物体进行关联。
- 最后是对追踪的结果进行评估。现在的方法想估计3D的效果都是在2D的空间上计算MOTA，MOTP。



现在3D MOT的大坑：（似乎是接下来挖了很多坑让人填，但是我发现不少坑Xinshuo Wen自己先填了一波，这个填坑速度真是让人佩服，比如多模块联合优化）

- 开源数据集的统一性
- 3D detection没有考虑传感器的物理特性，应该是说传感器有时候返回的数据有noise之类的
- 传感器融合冗余优化
- 检测和追踪需要耦合得更加好
- 物体表达上没有考虑场景和上下文
- 没有考虑传感器，预测，控制等等上下文的影响
- 3D MOT的数据集和指标目前有些问题，不能很好地表征出模型的好坏。



对于其中的metric和representation的问题，这篇paper先填两个大坑~ metric使用3D IoU就是把2D的IoU的metric计算类似的原理在3D tracking上做了计算，计算的是体积的重合度。



feature里面说到的问题是传统的办法都是用匈牙利算法计算association，这样的表达没有考虑objects之间的关系，然后她在模型里面加了feature interaction来进行这种场景的之间关系的communication。



接下来的其它大坑也被Xinshuo Wen小姐姐填了不少了，这个领域（deep vision）真就是要靠手速呀~ 晚一点点就没法发了。而且我还以为计算量这个会小一些，只有association了，3D tracking可以分开来计算，没想到现在都是多模块融合优化了，3D box都是10M+级别~ 现在没有集群计算力根本没法玩转自动驾驶的model了~



Ref:

> Xinshuo Wen: http://www.xinshuoweng.com
>
> 
>
> GNN3DMOT: Graph Neural Network for 3D Multi-Object Tracking with 2D-3D Multi-Feature Learning
>
> 
>
> 3D visual tracking video: https://www.youtube.com/watch?v=gN-fEfv02Dc
>
> 
>
> 3D MOT CVPR workshop: http://www.xinshuoweng.com/resources/slides/20200620_CVPR2020_Workshop_3DMOT.pdf

