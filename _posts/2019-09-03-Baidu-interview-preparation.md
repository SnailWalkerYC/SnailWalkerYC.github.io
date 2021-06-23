---
layout: post
title: Baidu interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



For the projects, see the DL/ML summary; CUDA summary; multi-threading.



For the coding part, see the below:

```c++
// 149. Max points on a Line
bool SameLine(const vector<int>& p1,
              const vector<int>& p2,
              const vector<int>& p3) {
  const int p21_x = p2[1] - p1[1];
  const int p21_y = p2[0] - p1[0];
  const int p31_x = p3[1] - p1[1];
  const int p31_y = p3[0] - p1[0];
  return p21_x * p31_y == p21_y * p31_x;
}  
  
int maxPoints(vector<vector<int>>& points) {
  int res = 1;
  
  for (int i = 0; i < points.size(); ++i) {
    for (int j = i + 1; j < points.size(); ++j) {
      int cur_len = 2;
      for (int k = j + 1; k < points.size(); ++k) {
        if (SameLine(points[i], points[j], points[k])) {
          ++cur_len;
        }
      }
      res = max(res, cur_len);
    }
  }
  
  return res;
}
```



```c++
// 74. Search in 2D matrix

```



```c++
// 965. Univalued Binary Tree
bool isUnivalTree(TreeNode* root, const int val = 100) {
  if (!root) return true;
  if (val != 100 && root->val != val) return false;
  return isUnivalTree(root->left, root->val) && isUnivalTree(root->right, root->val);
}
```



```c++
// Leetcode basic calculator series
```



```c++
// 51. N-Queens
```

