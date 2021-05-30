---
layout: post
title: number theory and brainteaser and minimax
date: 2021-07-12
categories: [technology]
tags: [tech]
comments: false
---



### Number Theory

```c++
// High accuracy
// Template
// 1. Store with int array.
// 2. 

// 1819. Number of Different Subsequences GCDs
int countDifferentSubsequenceGCDs(vector<int>& nums) {
  vector<bool> record(200001, false);
  int ans = 0;
  for (const auto& num : nums) record[num] = true;
  for (int i = 1; i <= 200000; ++i) {
    int fst = -1;
    for (int j = i; j <= 200000; j += i) {
      if (record[j]) {
        if (fst == -1) fst = j;
        else fst = __gcd(fst, j);
      }
    }
    ans += fst == i;
  } 
      
  return ans;
}
```



### Brainteaser





### Minimax

```c++
// 292. Nim Game stone 3 taken
// n%4 == 0

// 294. Flip Game II "++++"
bool canWin(string s) {
  for (int i = 1; i < s.size(); ++i) {
    if (s[i-1] == '+' && s[i] == '+' && !canWin(s.substr(0, i-1) + "--" + s.substr(i+1)))
      return true;
  }   
  return false;
}
```



