---
layout: post
title: Waymo interview preparation
date: 2019-08-04
categories: [technology]
tags: [coding]
comments: false
---

- General Coding

```c++
// Coding
// 792. Number of Matching Subsequences
int numMatchingSubseq(string s, vector<string>& words) {
  vector<queue<string>> record(26);
  int res = 0;
  for (const auto& word : words) {
    record[word[0]-'a'].push(word);
  }
  for (const auto& c : s) {
    const int size = record[c-'a'].size();
    for (int i = 0; i < size; ++i) {
      const auto sub_str = record[c-'a'].front().substr(1);
      record[c-'a'].pop();
      if (sub_str.empty())
        ++res;
      else 
        record[sub_str[0]-'a'].push(sub_str);
    }
  }
  return res;
}

// 644. Maximum Average Subarray II

// 64. Minimum Path Sum
int minPathSum(vector<vector<int>>& grid) {
  if (!grid.size() || !grid[0].size()) return 0;
  int row = grid.size();
  int col = grid[0].size();
  vector<int> last_record(col, INT_MAX);
  last_record[0] = 0;
  for (int r = 0; r < row; ++r) {
    last_record[0] += grid[r][0];
    for (int c = 1; c < col; ++c) {
      last_record[c] = min(last_record[c], last_record[c-1]) + grid[r][c];
    }
  }
  return last_record[col-1];
}

// Rain drop

// Compress of 2D array
```

- Multi-threads/CUDA
```c++
// Multi-threads/CUDA
// See the thread/CUDA part.
```


- Geometry
```c++
// Geometry
// point in the polygon

// area of the polygon

// Convex calculation

// IOU

// Cross product

// Dot product

// 找到3个最近的点，并且移除。这个要求是online的算法。也就是说每次添加一个点，都要update一下。距离远近就是threshold

// More in the geometry summary topic of coding
```

- Calibration

```c++
// Calibration
// Camera calibration

// LiDAR-camera calibration
```


- SLAM
```c++
// SLAM
```

- ML/DL

```c++
// ML/DL
Detect driving behavior is risky or not? Could be used for the prediction & planner.
Data collection questions: the risky data is rare, how to handle? 
What features would you use? 
Different number of objects detected, how to handle?
How do you build the model?  
Data from 3 types: rural, suburban, urban: train on one type and train on 3 types, the model from 3 types perform worse on 1 type of data than the model from 1 type. Is it possible? Why? How to solve?   
Batch normalization?  
How to solve the unbalanced label? See the DL summary part.  
```







