---
layout: post
title: hash rolling hash
date: 2021-06-17
categories: [technology]
tags: [tech]
comments: false
---



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

