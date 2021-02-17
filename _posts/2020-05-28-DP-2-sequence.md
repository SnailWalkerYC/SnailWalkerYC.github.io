---
layout: post
title: DP 2 (Two Sequence)
date: 2020-05-28
categories: [technology]
tags: [coding]
comments: false
---



```c++
// 1143. Longest Common Subsequence
// Status: record[i][j] -> longest common subsequence of in previous i of text1 and previous j of text2
// Status transform: record[i][j] = max(s[i] == s[j] + record[i-1][j-1], max(record[i-1][j], record[i][j-1]))
// Initialization: all as 0
// Return value: return record[len1][len2]
int longestCommonSubsequence(string text1, string text2) {
  int len1 = text1.size();
  int len2 = text2.size();
  vector<vector<int>> record(len1 + 1, vector<int>(len2 + 1, 0));
  for (int i = 0; i < len1; ++i) {
    for (int j = 0; j < len2; ++j) {
      int increment = text1[i] == text2[j] ? 1:0;
      record[i+1][j+1] = max(record[i][j] + increment, 
                         max(record[i][j+1], record[i+1][j]));
    }
  }
  return record[len1][len2];
}

// 72. Edit Distance
// Status: record[i][j] -> minimum operation for previous i+1 substr of word1 and j+1 substr of word2.
// Status transform: record[i][j] <- min(w1[i] != w[j] + record[i-1][j-1], min(record[i-1][j], record[i][j-1]))
// Initial value: add a special char here. "#". And initialize as i.
// Return: record[w1.size()-1][w2.size()-1]
int minDistance(string word1, string word2) {
  vector<vector<int>> record(word1.size() + 1, 
                             vector<int>(word2.size() + 1, 0));
  word1 = "#" + word1;
  word2 = "#" + word2;
  for (int i = 1; i < word1.size(); ++i) 
    record[i][0] = i;
  for (int i = 1; i < word2.size(); ++i)
    record[0][i] = i;
  for (int i = 1; i < word1.size(); ++i) {
    for (int j = 1; j < word2.size(); ++j) {
      record[i][j] = min(record[i-1][j-1] + ((word1[i] == word2[j])?0:1), 
                         min(record[i][j-1] + 1, record[i-1][j] + 1));
    }
  }
  return record[word1.size()-1][word2.size()-1];
}

// 115. Distinct Subsequences
// 
```