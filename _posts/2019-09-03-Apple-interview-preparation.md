---
layout: post
title: Apple interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



```c++
// BST serialization/deserialization

// design text classification system

// How explain how to implement a payment Services based on your experirnce

// 82. Remove Duplicates from Sorted List II

// 83. Remove Duplicates from Sorted List

// 33. Search in Rotated Sorted Array

// 528. Random Pick with Weight
class Solution {
 public:
  vector<int> accumulate_sum_;
  vector<int> record_;  
  Solution(vector<int>& weights) {
    for (const auto w : weights) {
      accumulate_sum_.push_back(accumulate_sum_.empty()?w : accumulate_sum_.back()+w);
    }
    record_ = vector<int>(weights.size(), 0);  
  }  
  int pickIndex() {
    const int random_weight = accumulate_sum_.back() * (static_cast<double>(rand()) / RAND_MAX);
    const int index = upper_bound(accumulate_sum_.begin(), accumulate_sum_.end(), random_weight) - begin(accumulate_sum_);    
    return min(index, (int)accumulate_sum_.size());  
  }
};
```



```c++
// Calibration of camera.

```

