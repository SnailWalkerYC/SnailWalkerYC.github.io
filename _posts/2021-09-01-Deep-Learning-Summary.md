---
layout: post
title: Deep Learning Summary
date: 2021-09-01
categories: [technology]
tags: [tech]
comments: false
---



### Forward & Backward Propagation



###  Component





### Unbalance Label

-  Re-sampling: over sampling on small size label
- Synthetic Samplings: add Gaussian noise into this data; interpolation. **SMOTE**
- Re-weighting: according to the inverse label size
- Transfer leaning: large sampling to train the network, small network, use the large sampling model to teach the samll network
- **metric learning** 
- Decoupling of representation & classifier.

[如何处理稀疏学习](https://www.zhihu.com/question/372186043)



#### Deep Learning Fin-tune skills

```c++
// Net depth will decide the learn ability. And width will decide the infomraiton of this layer.

// Perception field will be larger in the end.

// The pooling will be in the previous layers with higher frequency.

// Initialization is very important, the normal initialization will be much better than xavier.
// https://zhuanlan.zhihu.com/p/69026430

// 1 x N convolution, could improve the perception field.

// Learning first -> add BN/data augmentation -> adjust layers/convolution.

// Loss is better than accuracy

// receptive field decide.

// Training set & validation set, early stop, over-fitting/under-fitting.

// ReLU/Leaky-ReLU/Sigmoid/Tanh.

// Overfitting -> L1, L2 norm.

// Optimizer choose: Adagrad, Adadelta, RMSprop, Adam -> Adam

// Residual block & BN layer.

// relu激活函数初始化推荐使用He normal，tanh初始化推荐使用Glorot normal，其中Glorot normal也称作Xavier normal初始化；数据预处理方法一般也就采用数据归一化即可。

// Data Preprocessing

// New scenarios will cause the covariate shift. Training set distribution change; cross validation; expand test set; https://www.zhihu.com/question/289025990 
```

