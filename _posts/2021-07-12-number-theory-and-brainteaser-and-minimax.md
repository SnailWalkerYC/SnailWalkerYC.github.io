---
layout: post
title: number theory and brainteaser and minimax
date: 2021-07-12
categories: [technology]
tags: [tech]
comments: false
---





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



