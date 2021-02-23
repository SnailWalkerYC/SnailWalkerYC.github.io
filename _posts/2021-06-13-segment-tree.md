---
layout: post
title: tree segment tree
date: 2021-06-13
categories: [technology]
tags: [tech]
comments: false
---





线段树是进行区间信息查询维护和修改的数据结构。

```c++
// 1353. Maximum Number of Events That Can Be Attended
// Segment tree solution:
static constexpr int n_ = 100000;
int arr_[4*n_+1] = {0};  

int Build(const int index, const int left, const int right) {
  if (left == right) {
    return arr_[index] = left;
  }
  const int mid = left + (right - left)/2;
  const int l = Build(index*2, left, mid);
  const int r = Build(index*2+1, mid+1, right);
  return arr_[index] = min(l, r);
}  
  
int Query(const int q_l, const int q_r, const int left, const int right,
          const int v) {
  if (q_l <= left && right <= q_r) {
    return arr_[v];
  }
  const int mid = left + (right - left)/2;
  if (q_l <= mid) {
    const int q_val = Query(q_l, q_r, left, mid, v*2);
    if (q_val != INT_MAX) return q_val;
  } 
  if (q_r > mid) {
    const int q_val = Query(q_l, q_r, mid+1, right, v*2+1);
    if (q_val != INT_MAX) return q_val;
  }
  return INT_MAX;
}  
  
void Update(const int pos, const int left, const int right, const int index) {
  if (left == right) {
    arr_[index] = INT_MAX;
    return;
  }
  const int mid = left + (right - left)/2;
  if (pos <= mid) {
    Update(pos, left, mid, index*2);
  } else {
    Update(pos, mid+1, right, index*2+1);
  }
  
  arr_[index] = min(arr_[index*2], arr_[index*2+1]);
}  
  
int maxEvents(vector<vector<int>>& events) {
  sort(begin(events), end(events), [](const vector<int>& e1, const vector<int>& e2){
    return e1[1] < e2[1] || (e1[1] == e2[1] && e1[0] < e2[0]);
  });
  int count = 0;
  Build(1, 1, n_);
  for (const auto& event : events) {
    const int ts = Query(event[0], event[1], 1, n_, 1);
    if (ts >= event[0] && ts <= event[1]) {
      ++count;
      Update(ts, 1, n_, 1);
    }
  }
  
  return count;
}
```



Ref：

1. [线段树](https://oi-wiki.org/ds/seg/)

