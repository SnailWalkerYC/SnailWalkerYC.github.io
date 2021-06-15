---
layout: post
title: Machine Learning Summary
date: 2019-08-04
categories: [technology]
tags: [coding]
comments: false

---



### Decision Tree

#### Metrics

- Gini Impurity

https://www.cnblogs.com/pinard/p/6053344.html

- Information Gain

$H(Y | X) = \sum_{x -> X} p(x) H(Y | X = x)$

[连续值处理](https://blog.csdn.net/u012328159/article/details/79396893)


#### Methods

- ID3 (iterative Dichotomiser 3)

https://zhuanlan.zhihu.com/p/26760551

**信息增益准则其实是对可取值数目较多的属性有所偏好！**

- C4.5 (successor of ID3)

https://zhuanlan.zhihu.com/p/26760551

Continuous number discrection.



- CART (classification & regression tree)

Using Gini as metric.

- MARS

- Conditional Inference Tree



**Constraint:**

Noise; over-fitting; NP hard

**Pros:**

Fix multi-dimensions classification; pruning.



Ref:

[Decision Tree Learning](https://en.wikipedia.org/wiki/Decision_tree_learning)

[Decision tree regression](http://www.saedsayad.com/decision_tree_reg.htm)

[Decision Tree Continuous Discrete](https://www.zhihu.com/question/32250553)

[Information Gain](https://zhuanlan.zhihu.com/p/26596036)

[Information Gain 2](https://zhuanlan.zhihu.com/p/26551798)

[Cross Entropy](https://zhuanlan.zhihu.com/p/26486223)

[Summary Decision tree](https://zhuanlan.zhihu.com/p/163303123)

[Why C4.5 could fix this](https://www.zhihu.com/question/425720956)

[Decision Tree](https://blog.csdn.net/suipingsp/article/details/41927247)

