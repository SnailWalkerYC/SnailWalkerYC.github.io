---
layout: post
title: heap priority queue
date: 2021-06-23
categories: [technology]
tags: [tech]
comments: false

---





### Heap

```c++
// 1675. Minimize Deviation in Array
class Solution {
 public:
  int minimumDeviation(vector<int>& nums) {
    priority_queue<int> record;
    int min_val = INT_MAX;
    int max_val = INT_MIN;
    int deviation = INT_MAX;
    for (const auto& num : nums) {
      const int new_num = (num & 1) ? num << 1 : num;
      record.push(new_num);
      min_val = min(new_num, min_val);
      max_val = max(new_num, max_val);
    }
    while (!record.empty() && !(record.top() & 1)) {
      deviation = min(deviation, record.top() - min_val);
      int tp = record.top() / 2;
      record.pop();
      record.push(tp);
      min_val = min(min_val, tp);
    }
    deviation = min(deviation, record.top() - min_val);
    return deviation;
  }
};
```

