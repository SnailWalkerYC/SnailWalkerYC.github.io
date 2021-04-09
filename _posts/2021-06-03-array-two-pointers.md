---
layout: post
title: array two pointer
date: 2021-06-03
categories: [technology]
tags: [tech]
comments: false
---



```c++
// 1793. Maximum Score of a Good Subarray
// Greedy & two pointers
// https://leetcode.com/problems/maximum-score-of-a-good-subarray/discuss/1108358/Python-Two-pointer-Similar-to-LC42.-Trapping-Rain-Water
// https://leetcode.com/problems/maximum-score-of-a-good-subarray/discuss/1108271/C%2B%2B-O(N)-Time-Greedy
// https://leetcode.com/problems/maximum-score-of-a-good-subarray/discuss/1108333/JavaC%2B%2BPython-Two-Pointers
int maximumScore(vector<int>& nums, int k) {
  int i = k - 1;
  int j = k + 1;
  int max_score = 0;
  while (true) {
    while (i >= 0 && nums[i] >= nums[k]) --i;
    while (j < nums.size() && nums[j] >= nums[k]) ++j;
    max_score = max(max_score, (j - i - 1)*nums[k]);
    if (i >= 0 && j < nums.size()) nums[k] = max(nums[i], nums[j]);
    else if (i < 0 && j >= nums.size()) break;
    else if (i < 0) nums[k] = nums[j];
    else nums[k] = nums[i];
  }
  return max_score;
}
```

