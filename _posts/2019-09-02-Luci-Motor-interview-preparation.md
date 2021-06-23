---
layout: post
title: Lucid Motor and Autel interview preparation
date: 2019-09-02
categories: [technology]
tags: [coding]
comments: false
---



## CUDA

Cuda reduction: see cuda summary part.



```c++
// Leetcode 776. Split BST
vector<TreeNode*> splitBST(TreeNode* root, int target) {
  vector<TreeNode*> ans(2, nullptr);
  if (!root) return ans;
  
  if (root->val > target) {
    ans[1] = root;
    const auto res = splitBST(root->left, target);
    root->left = res[1];
    ans[0] = res[0];
  } else {
    ans[0] = root;
    const auto res = splitBST(root->right, target);
    root->right = res[0];
    ans[1] = res[1];
  }
  
  return ans;
}
```





