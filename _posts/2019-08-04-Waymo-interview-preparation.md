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
bool Check(vector<int>& nums, const double ave, const int k) {
  double acc_sum = 0.0;
  for (int i = 0; i < k; ++i) 
    acc_sum += nums[i] - ave;
  if (acc_sum >= 0) return true;
  double pre_sum = 0.0;
  double min_sum = 0.0;
  for (int i = k; i < nums.size(); ++i) {
    acc_sum += nums[i] - ave;
    pre_sum += nums[i-k] - ave;
    min_sum = min(min_sum, pre_sum);
    if (acc_sum >= min_sum)
      return true;
  }
  return false;
}  
double findMaxAverage(vector<int>& nums, int k) {
  double left = -10001.0;
  double right = 10001.0;
  while (right - left > 1.0e-5) {
    const double ave = (left + right) / 2.0;
    if (Check(nums, ave, k)) {
      left = ave;
    } else {
      right = ave;
    }
  }
  return left;
}

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

// 1293. Shortest Path in a Grid with Obstacles Elimination
int shortestPath(vector<vector<int>>& grid, int k) {
  if (grid.size() <= 1 && grid[0].size() <= 1)
    return 0;
  const int row = grid.size();
  const int col = grid[0].size();
  const int size = row * col;
  unordered_set<int> visited;
  visited.insert(0);
  queue<vector<int>> cur_visiting;
  cur_visiting.push({0, 0, 0, 0});
  const int dirs[2][4] = {{0, 1, 0, -1}, {-1, 0, 1, 0}};
  
  while (!cur_visiting.empty()) {
    const auto cur = cur_visiting.front();
    cur_visiting.pop();
    if (cur[0] == row - 1 && cur[1] == col - 1) {
      return dis;
    }
    else {
      for (int i = 0; i < 4; ++i) {
        const int new_row = cur[0] + dirs[1][i];
        const int new_col = cur[1] + dirs[0][i];
        
        if (new_row < 0 || new_col < 0 || new_row >= row ||
            new_col >= col) 
          continue;
        const auto key = cur[3] * size + col * new_row + new_col;
        if (grid[new_row][new_col]) {
          if (cur[3] >= k) continue;
          if (visited.count(key + size) > 0) continue;
          visited.insert(key + size);
          cur_visiting.push({new_row, new_col, cur[2] + 1,
                             cur[3] + 1});
        } else {
          if (visited.count(key) > 0) continue;
          cur_visiting.push({new_row, new_col, cur[2] + 1,
                             cur[3]});
          visited.insert(key);
        }
      }
    }
  }
  return -1;
}

// Rain drop
// https://www.1point3acres.com/bbs/interview/google-software-engineer-176388.html
// With map/merge intervals.

// Compress of 2D array
// https://leetcode.com/problems/rank-transform-of-a-matrix/
int Find(int idx, vector<int>& p) {
      if (idx != p[idx]) {
        p[idx] = Find(p[idx], p);
      }
      return p[idx];
    }
  
    vector<vector<int>> matrixRankTransform(vector<vector<int>>& matrix) {
      int rows = matrix.size();
      int cols = rows?matrix[0].size() : 0;
      map<int, vector<int>> points;
      for (int i = 0; i < rows; ++i) {
        for (int j = 0; j < cols; ++j) {
          points[matrix[i][j]].push_back(i * cols + j);
        }
      }
      vector<int> rank(rows + cols, 0);
      for (const auto& pts : points) {
        vector<int> p(rows + cols, 0);
        iota(p.begin(), p.end(), 0);
        for (const auto& pt : pts.second) {
          const int i = Find(pt / cols, p);
          const int j = Find(pt % cols + rows, p);
          p[j] = i;
          rank[i] = max({rank[j], rank[i]});
        }
        for (const auto& pt : pts.second) {
          const auto &cur_rank = Find(pt/cols, p);
          matrix[pt/cols][pt%cols] = rank[cur_rank] + 1;
          rank[pt/cols] = rank[cur_rank] + 1;
          rank[pt%cols + rows] = rank[cur_rank] + 1;
        }
      }
      return matrix;  
    }
```

- Multi-threads/CUDA
```c++
// Multi-threads/CUDA
// See the thread/CUDA part.
```


- Geometry
```c++
// Geometry
// Convex Polygon, no internal angle more than 180.
// https://www.mathsisfun.com/geometry/polygons.html

// point in the polygon
// Area

// area of the polygon
// Calculate with cross product.

// Convex calculation
// See the geometry topic.

// IOU
overlapped_area/total_area

// Cross product, area & norm of a plane.
// https://web.ma.utexas.edu/users/m408m/Display12-5-4.shtml  
a X b = || a || * || b || * sin(theta)  

// Dot product, projection product
a b = || a || * || b || * cos(theta)  

// line & plane angle
// with plane normal vector.  
  
// 找到3个最近的点，并且移除。这个要求是online的算法。也就是说每次添加一个点，都要update一下。距离远近就是threshold
// 使用两个priority_queue
// http://www.whudj.cn/?p=920
// https://zhuanlan.zhihu.com/p/357052001  
  
// More in the geometry summary topic of coding
```

- Calibration

```c++
// Calibration
// Camera calibration
// Ref: https://blog.csdn.net/Dang_boy/article/details/79403835
// Learning OpenCV
// Calculate the homography for each image[ Homography ] with DLT(direct linear transform). Needs 4 pair of points, with 8 constrain.
// least square: https://zhuanlan.zhihu.com/p/33899560 rotate.
// RANSAC: https://blog.csdn.net/luoshixian099/article/details/50217655#:~:text=RANSAC%E7%AE%97%E6%B3%95%E4%BB%8E%E5%8C%B9%E9%85%8D%E6%95%B0%E6%8D%AE,%E5%AF%B9%E5%BA%94%E7%9A%84%E4%BB%A3%E4%BB%B7%E5%87%BD%E6%95%B0%E6%9C%80%E5%B0%8F%E3%80%82
// 1. Random select and construct the model
// 2. Using the model to find closer points
// 3. Using new points to estimate the model

// LiDAR-camera calibration
Extrinsic Calibration of Lidar and Camera with Polygon.
// PnP. perspective n points.
// 1. DLT; 2. P3P; 3. Bundle Adjust -> to minimize the loss of re-projection error. Using Lie Algebra & Gaussian Newton method(large size of Hessian matrix) & Levenberg-Marquardt.   
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







