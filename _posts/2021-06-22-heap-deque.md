---
layout: post
title: heap deque
date: 2021-06-22
categories: [technology]
tags: [tech]
comments: false
---



### Deque

- deque的实现原理

使用一个list的指针，每个指针指向一个vector的数组。固定大小。

- deque的操作

可以push_front， push_back。可以front和back找到开始和结尾的数据。可以pop_front和pop_back进行数据操作。同时可以进行类似于vector的数组索引。近乎全能的STL。

[参考](https://www.cplusplus.com/reference/deque/deque/)

```c++
// 239. Sliding Window Maximum
// Monotonic queue
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
  std::deque<int> history;
  std::vector<int> max_arr;
        
  for (int idx = 0; idx < nums.size(); ++idx) {
    if (!history.empty() && history.front() == idx - k) {
      history.pop_front();  
    }
    while (!history.empty() && nums[history.back()] <= nums[idx]) {
      history.pop_back();
    }    
    history.push_back(idx);
    if (idx >= k - 1) {
      max_arr.push_back(nums[history.front()]);
    }  
  }  
  return max_arr;  
}

// 1670. Design Front Middle Back Queue
// Two deque solution for O(1) each operation
class FrontMiddleBackQueue {
 public:
  std::deque<int> left_part;
  std::deque<int> right_part;
    
  FrontMiddleBackQueue() {}
    
  void pushFront(int val) {
    left_part.push_front(val);
    if (left_part.size() - 1 > right_part.size()) {
      right_part.push_front(left_part.back());
      left_part.pop_back();  
    }
  }
    
  void pushMiddle(int val) {
    if (left_part.size() <= right_part.size()) {
      left_part.push_back(val);
    } else {
      right_part.push_front(left_part.back());
      left_part.pop_back();
      left_part.push_back(val); 
    }
  }
    
  void pushBack(int val) {
    right_part.push_back(val);
    if (right_part.size() > left_part.size()) {
      left_part.push_back(right_part.front());
      right_part.pop_front();  
    }  
  }
    
  int popFront() {
    if (left_part.empty() && right_part.empty()) return -1;   
    int val = left_part.front();
    left_part.pop_front();
    if (left_part.size() < right_part.size()) {
      int val2 = right_part.front();
      right_part.pop_front();
      left_part.push_back(val2);  
    }  
    return val;
  }
    
  int popMiddle() {
    if (left_part.empty() && right_part.empty()) return -1;   
    const int val = left_part.back();
    left_part.pop_back();
    if (left_part.size() < right_part.size()) {
      const int val2 = right_part.front();
      right_part.pop_front();
      left_part.push_back(val2);  
    }  
    return val;  
  }
    
  int popBack() {
    if (left_part.empty() && right_part.empty()) return -1;
    int val;
        
    if (right_part.empty()) {
      val = left_part.front(); 
      left_part.pop_back();
      return val;  
    }  else {
      val = right_part.back();
      right_part.pop_back();
      if (left_part.size() - 1 > right_part.size()) {
        int val2 = left_part.back();
        left_part.pop_back();
        right_part.push_front(val2);  
      }  
      return val;  
    }
  }
};

// 1673. Find the Most Competitive Subsequence
vector<int> mostCompetitive(vector<int>& nums, int k) {
  deque<int> record;
  for (int i = 0; i < nums.size(); ++i) {
    while (!record.empty() && record.back() > nums[i]
           && record.size() + nums.size() - i > k) {
      record.pop_back();
    }
    record.push_back(nums[i]);
  }
  
  vector<int> ans;
  for (int i = 0; i < k; ++i) {
    ans.push_back(record.front());
    record.pop_front();
  }
  return ans;
}

// 862. Shortest Subarray with Sum at Least K
  vector<int> acc_sum(A.size()+1, 0);
  for (int i = 0; i < A.size(); ++i) {
    acc_sum[i+1] = acc_sum[i] + A[i];
  }
  deque<int> record({0});
  int ans = INT_MAX;    
  for (int i = 1; i < acc_sum.size(); ++i) {
    while (!record.empty() && acc_sum[i] - acc_sum[record.front()] >= K) {
      ans = min(i - record.front(), ans);
      record.pop_front();
    }
    while (!record.empty() && acc_sum[record.back()] >= acc_sum[i]) {
      record.pop_back();
    }
    record.push_back(i);
  }
      
  return ans == INT_MAX?-1:ans;
}
```


