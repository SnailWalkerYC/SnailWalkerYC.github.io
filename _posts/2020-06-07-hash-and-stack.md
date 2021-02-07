---
layout: post
title: hash and stack
date: 2020-06-07
categories: [technology]
tags: [tech]
comments: false
---



### Hash

```c++
// 128. Longest Consecutive Sequence
class Solution {
 public:
  int Count(unordered_map<int, int>& record, int num) {
    if (record.find(num) != record.end() && record[num] > 0)
      return record[num];
    if (record.find(num) == record.end()) return 0;
    
    int count = 1;
    count += Count(record, num+1);
    record[num] = count;
    return count;
  }
  
  int longestConsecutive(vector<int>& nums) {
    int longest_num = 0;
    unordered_map<int, int> record;
    for (const auto num:nums) {
      record[num] = 0;
    }
    for (const auto [num, times] : record) {
      if (times > 0) continue;
      longest_num = max(longest_num, Count(record, num));
    }
    return longest_num;
  }
};


```

