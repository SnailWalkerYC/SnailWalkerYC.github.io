---
layout: post
title: array-sequence
date: 2021-06-02
categories: [technology]
tags: [tech]
comments: false

---



Some tricks: prefix sum -> prefix porduct



### k elements

```c++
// 1352. Product of the Last K Numbers
class ProductOfNumbers {
 private:
  vector<int> record_;
  
 public:
  ProductOfNumbers() {
    record_.reserve(40000);
    record_ = {1};
  }
    
  void add(int num) {
    if (!num) {
      record_ = {1};
    } else {
      record_.emplace_back(record_.size() ? record_.back() * num : num);
    }
  }
    
  int getProduct(int k) {
    return record_.size() > k? record_.back() / record_[record_.size() - k - 1] : 0;      
  }
};
```

