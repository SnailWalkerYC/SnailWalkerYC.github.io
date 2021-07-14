---
layout: post
title: Machine Learning Summary
date: 2021-09-01
categories: [technology]
tags: [tech]
comments: false
---



# Metric

### AUC

[PR-AUC](https://towardsdatascience.com/how-to-efficiently-implement-area-under-precision-recall-curve-pr-auc-a85872fd7f14)

[The recall & precision plot, area under curve.](https://jonathan-hui.medium.com/map-mean-average-precision-for-object-detection-45c121a31173) 

### ROC

[ROC AUC](https://neptune.ai/blog/f1-score-accuracy-roc-auc-pr-auc) -> FPR(x), TPR(y) [Receiver operating characteristic](https://towardsdatascience.com/understanding-auc-roc-curve-68b2303cc9c5)

TPR(recall, sensitivity). FPR = 1 - sensitivity.

**calculate ROC AUC on predicted scores** 

AUC - ROC curve is a performance measurement for the classification problems at various threshold settings. ROC is a probability curve and AUC represents the degree or measure of separability. It tells how much the model is capable of distinguishing between classes. Higher the AUC, the better the model is at predicting 0 classes as 0 and 1 classes as 1. By analogy, the Higher the AUC, the better the model is at distinguishing between patients with the disease and no disease.

The ROC curve is plotted with TPR against the FPR where TPR is on the y-axis and FPR is on the x-axis.

### F1

Precison = TP / (TP + FP)

Recall = TP / (TP + FN)

F1 = 2 * Precision * Recall / (Precision + Recall)

Accuracy = (TP + TN) / (TP + FP + TN + FN)

### AP(average precision)

area of recall(x) and prevision(y)

Interpolated AP.

# Supervised Learning

## Decision Tree

```c++
// Write Cross Entropy 
```



## SVM

```c++
// SVM分开的点投影到分割平面之后还是可分的吗

// xgboost与gbdt的区别与联系

// transformer在bert中的应用

// 特征工程方法有哪些

// 锁知道哪些

// c++ share_ptr怎么解决循环引用

// c++ 多重继承

// factorization machine


```





# Non Supervised Learning



## Cluster



### k means

```c++
/* point cluster: 给一个list of points, ex. [0, 1], [0, 2], [0, 3], 还有一个int k. 点跟点距离 less or equal to k 就是在同一个cluster
effect 是transitive. [0, 1], [0, 2] 距离为1, [0,2], [0, 3]距离为1, 所以[0, 1], [0, 2], [0, 3] 在同一个cluster
求总共有几个cluster & 在每个cluster里的点 */
// Like k-means.
```



### NMS



### IOU



# Trick

## Regularization

[L1 & L2 regularization](https://towardsdatascience.com/ridge-and-lasso-regression-a-complete-guide-with-python-scikit-learn-e20e34bcbf0b)

