---
layout: post
title: divide and conquer
date: 2021-07-07
categories: [technology]
tags: [tech]
comments: false
---

### Divide and Conquer

```c++
// 1755. Closest Subsequence Sum
class Solution {
 public:
  void GetSum(const vector<int>& nums, const int target, const int idx, 
              const int cur, unordered_set<int>& s) {
    if (idx > target) {
      s.insert(cur);
      return;
    }
    GetSum(nums, target, idx+1, cur, s);
    GetSum(nums, target, idx+1, cur + nums[idx], s);
  }
  
  int minAbsDifference(vector<int>& nums, int goal) {
    unordered_set<int> sum1;
    unordered_set<int> sum2;
    
    GetSum(nums, nums.size()/2, 0, 0, sum1);
    GetSum(nums, nums.size() - 1, nums.size()/2+1, 0, sum2);
    
    vector<int> half_vec(sum1.begin(), sum1.end());
    sort(half_vec.begin(), half_vec.end());
    
    int min_val = INT_MAX;
    for (const auto& num : sum2) {
      int idx = lower_bound(half_vec.begin(), half_vec.end(), 
                            goal - num) - half_vec.begin(); 
      if (idx < half_vec.size()) 
        min_val = min(min_val, abs(goal - half_vec[idx] - num));
      if (idx > 0)
        min_val = min(min_val, abs(goal - half_vec[idx-1] - num));
    }
    return min_val;
  }
};
```

