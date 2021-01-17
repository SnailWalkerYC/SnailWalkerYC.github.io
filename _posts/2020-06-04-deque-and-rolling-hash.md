---
layout: post
title: deque and rolling hash
date: 2020-06-04
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
```



### Rolling Hash

See book Introduction to Algorithm in P582 with presudo code.

```c++
// 1698. Number of Distinct Substrings in a String
int countDistinct(string s) {
  int num_dist = 0;
  const long long mod = 1.0e9 + 9; 
  long long base = 1;
  long long s_hash = 0;
  long long res = 0;
      
  for (int i = 0; i < s.size(); ++i) {
    s_hash = (s_hash * 26 + (s[i] - 'a' + 1)) % mod;
    base = base * 26 % mod;
    unordered_set<int> record;
    record.insert(s_hash);
    long long hash = s_hash;
    for (int j = i+1; j < s.size(); ++j) {
      hash = (26 * hash - (base* (s[j - i - 1] - 'a' + 1)) % mod + s[j] - 'a' + 1 + mod) % mod;
      record.insert(hash);
    }
    res += record.size();
  }
      
  return res;
}

// 1147. Longest Chunked Palindrome Decomposition
int longestDecomposition(string text) {
  int text_len = text.size();
  int stt_left = 0;
  int stt_right = text_len - 1;
  int num_chunk = 0;
  const long long mod = 1.0e9 + 9;
  while (stt_left <= stt_right) {
    long long hash_left = 0;
    long long hash_right = 0;
    long long base = 1;
    for (int i = 0; i <= stt_right - stt_left; ++i) {
      hash_left = (hash_left * 26 + (text[i+stt_left] - 'a')) % mod;
      hash_right = (hash_right + (text[stt_right - i] - 'a') * base) % mod;
      base = base * 26 % mod;
      if (hash_left == hash_right) {
        num_chunk += (i == stt_right - stt_left) ? 1 : 2;
        stt_left += i + 1;
        stt_right -= i + 1;
        break;
      }
    }
  }
        
  return num_chunk;
}

// 1316. Distinct Echo Substrings
int distinctEchoSubstrings(string text) {
  long long s_hash = 0;
  const long long mod = 1.0e9 + 9;
  long long base = 1;
  int res = 0;  
  for (int i = 0; i <= text.size()/2; ++i) {
    s_hash = (s_hash * 26 + text[i] - 'a') % mod;
    base = base * 26 % mod;  
    long long hash = s_hash;
    vector<long long> record({s_hash}); 
    unordered_set<long long> exists;  
    for (int j = i + 1; j < text.size(); ++j) {
      hash = (hash * 26 - (base * (text[j - i - 1] - 'a'))%mod + mod + text[j] - 'a') % mod;
      if (record.size() > i) {
        if (hash == record[j-2*i-1]) {
          const auto status = exists.insert(hash);
          if (status.second) {
            ++res;
          }
        }
      }
      record.push_back(hash);  
    }  
  }  
  return res;  
}
```

