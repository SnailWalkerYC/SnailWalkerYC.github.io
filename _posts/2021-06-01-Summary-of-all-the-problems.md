---
layout: post
title: Summary of all the problems
date: 2021-06-01
categories: [technology]
tags: [tech]
comments: false
---



程序由数据结构和算法组成，一个程序的输入和输出已经给定，剩余的就是如何排列组合数据结构和算法进行一个图搜索来找到最佳组合。



**目前构造的思维导图如下：**

![Algorithm Classification](../images/leetcode.png)



# Data Structure

数据结构基本上分为**数组、链表、树、哈希、栈、堆、设计**。




## Array 

数组类知识点主要在于如何对于随机读取的线性结构进行数据的过滤处理，请看[Array Introduction](http://yuchenspace.info/array/)



## Linked List

链表主要是对于数据合并可以进行很好的操作，不过如果是unordered_map + list这种双链表结构，是可以实现O(1)读取的。请看 [Linked List Summary](http://yuchenspace.info/linked-list-summary/)



## Tree

树是进行二分搜索查找极好的数据结构。比较高阶的就有：binary search tree，trie，binary indexed tree，segment tree。请看总结：[Tree](http://yuchenspace.info/tree/)



## Hash 

哈希本质就是进行快速索引，以及对键值进行某种程度的管理。具体情况：[Hash](http://yuchenspace.info/hash/)



## Stack

Stack栈，是一种后进先出的数据结构，在进行递归的时候，很适合使用，请看 [Stack](http://yuchenspace.info/stack/)。



## Heap

Heap是一种先进先出的数据结构，当然经过了改进，后面有了双向的deque和极值在堆顶的特殊结构，请看[Heap](http://yuchenspace.info/heap/)。



## Design

系统设计其实是算法和数据结构的结合，但是也可以理解为对各种数据组合之后创造了新的数据结构，这个数据结构支持某些特殊的操作，具体请看：[Design](http://yuchenspace.info/design/).



# Algorithms

算法涉及大类，DP、greedy、graph、binary search、sort、divide & conquer。

 

## DP

动态规划进行记忆化搜索来加速算法，请看[DP](http://yuchenspace.info/dp/)。



## Greedy

贪心算法是用来在求解最优化问题中，所求问题正好是可以使用局部最优解作为最终的最优解的问题的处理。具体请看: [Greedy](http://yuchenspace.info/greedy/)



## Graph

图是很大的一个话题，图用于构造不同object之间的联系，来获取其中联系的某些特征。具体看：[Graph](http://yuchenspace.info/graph/)



## Binary Search

二分搜索是搜索目标，有些骚操作可以转化问题求解搜索的最佳答案。请看：[Binary Search](http://yuchenspace.info/binary-search/)



## Sort

排序是个很有用的东西，C++里面有集成的排序了，还有稳定排序。请看：[sort](http://yuchenspace.info/sort/)



## Divide & Conquer

分而治之是一种编程集大成的思想，具体参考: [Divide & Conquer](http://yuchenspace.info/divide-and-conquer/)



# Math

数学也是经常考的一个专题，这个专题目前总结了位操作、几何学、扫描线、概率、数学理论。



## Bit manipulation

位操作，进行各种位操作，可以实现意想不到的组合，具体请看：[Bit Manipulation](http://yuchenspace.info/bit-manipulation/)



## Geometry

几何学方面，对于求取轮廓之类的有较大的帮助，具体可以看：[Geometry](http://yuchenspace.info/geometry/)



## Line Sweep

扫描线对于求解区间问题是个大杀器，可以看：[Line Sweep](http://yuchenspace.info/line-sweeping/)



## Probability 

这里主要是求解各种随机数，可以进行各种采样。请看: [Probability](http://yuchenspace.info/reject-sampling-and-reservoir-sampling-and-random/)



## Number Theory

数学理论涉及各种数学的操作，脑筋急转弯等等，具体请看： [Theory](http://yuchenspace.info/number-theory-and-brainteaser-and-minimax/)

