---
layout: post
title: computational advertising vs recommendation system 
date: 2021-09-03
categories: [technology]
tags: [life]
comments: false
---



**CA:** copmuational advertising

**RS:** recommendation system

## optimize target

CA is more focusing on three targets: advertisers, users, media. 站的角度更多的是用这个CTR模型产生更多的收益，那么在使用的时候就更偏向于将CTR高的广告全都排在前面。The central problem of computational advertising is to find the "best match" between a given user in a given context and a suitable advertisement. 优化CPM。



RS is focusing on user experience. 在使用CTR模型的时候，就不适宜全部按照CTR来进行内容排序。而是要兼顾内容的多样性，新颖性，和流行度。因为推荐系统的核心是用户体验，如果涸泽而渔的全部用根据用户行为历史生成的推荐内容，很容易让用户疲倦、透支用户的兴趣。那么为了照顾用户的整体体验并挖掘用户的长期兴趣，就要考虑用户在不同体验阶段的感受和行为特点。优化CTR。



## general methods

CA: 广告主和媒体关于价格的博弈，广告主之间的价格博弈，正是这种博弈的存在，使得计算广告一定要去关注基于博弈论的bidding策略。而且为了“协调”广告主、媒体的相关操作，诞生了支持性的相应模块，去处理广告排期、定向、预算控制等模块，这里面当然涉及大量算法和模型，比如处理广告排期需要最优化理论的支持，定向需要各类预测模型，预算控制需要控制论的一些知识等等。这些都是推荐系统里不存在的。CTR模型，Bidding策略，yield optimization（也许可以叫排期优化），智能预算控制。在广告主、媒体、用户利益之间寻找平衡， 匹配出的结果相关性只是一方面，另外还要考虑直接的商业利益。DSP\DMP\SSP\RTB\ADX

RS: CTR模型及其他推荐模型，探索与利用（exploitation&exploration），冷启动问题，数据有偏问题。面向用户提供新鲜、相关、有趣的话题/产品，增强用户粘性， 增加产品的流量和活跃度。



**Ref link** [1](https://www.zhihu.com/question/19662693) [2](https://www.zhihu.com/topic/19591877/hot)

