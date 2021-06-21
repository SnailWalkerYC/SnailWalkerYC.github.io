---
layout: post
title: Data Structure & Algorithms Fast Version
date: 2019-09-09
categories: [technology]
tags: [coding]
comments: false
---



**Practictice makes perfect.**



# Basic Algorithm



## Prefix Sum

```c++
// 1906. Minimum Absolute Difference Queries
int record[100001][101];  
vector<int> minDifference(vector<int>& nums, vector<vector<int>>& queries) {
  for (int i = 0; i < nums.size(); ++i) {
    for (int j = 1; j <= 100; ++j) {
      record[i+1][j] = record[i][j] + (nums[i] == j);
    }
  }
  vector<int> ans;
  for (const auto& query : queries) {
    const int left = query[0];
    const int right = query[1] + 1;
    int t = 101;
    int cur_min = 101;
    for (int i = 1; i <= 100; ++i) {
      if (record[right][i] > record[left][i]) {
        if (t <= 100) {
          cur_min = min(cur_min, i - t);
        }
        t = i;
      }
    }
    ans.emplace_back(cur_min > 100?-1:cur_min);
  }
  return ans;
}
```





