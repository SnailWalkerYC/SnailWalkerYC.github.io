---
layout: post
title: Pony ai interview preparation
date: 2019-08-02
categories: [technology]
tags: [coding]
comments: false
---

- Lexicographical Numbers

```c++
// 386. Lexicographical Numbers

```

- Lowest Common Ancestor

```c++
// 236. Lowest common ancestor of a Binary Tree
TreeNode* lowestCommonAncestor(TreeNode* root, 
                                   TreeNode* p, TreeNode* q) {
  if (!root || p == root || q == root) return root;
  const auto left = lowestCommonAncestor(root->left, p, q);
  const auto right = lowestCommonAncestor(root->right, p, q);
  
  return left && right? root:(left?left:right);
}

// 1644. Lowest Common Ancestor of a Binary Tree II
bool hasNode(TreeNode* root, TreeNode* p) {
  if (!root) return false;
  if (root == p) return true;
  return hasNode(root->left, p) || hasNode(root->right, p);
}  
  
TreeNode* result_ = nullptr; 
TreeNode* init_ = nullptr;  
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
  if (!root) return nullptr;
  if (!init_) init_ = root;
    
  if (root == p) {
    if (hasNode(p->left, q) || hasNode(p->right, q)) {
      result_ = p;
      return result_;
    } else {
      return root!=init_?root:nullptr;
    }
  }
  if (root == q) {
    if (hasNode(q->left, p) || hasNode(q->right, p)) {
      result_ = q;
      return result_;
    } else {
      return root!=init_?root:nullptr;
    }
  }
  
  const auto left = lowestCommonAncestor(root->left, p, q);
  const auto right = lowestCommonAncestor(root->right, p, q);
  
  if (result_) return result_;
  if (left && right) result_ = root;
  if (result_) return result_;
  
  return init_ != root? (left?left:right) : nullptr;
}

// 1605. Lowest common ancestor of a binary tree iii
Node* lowestCommonAncestor(Node* p, Node * q) {
  unordered_set<Node*> p_parents;
  while (p) {
    p_parents.insert(p);
    p = p->parent;
  }
  while (q) {
    if (p_parents.count(q) > 0) return q;
    q = q->parent;
  }
  return nullptr;
}
Node* lowestCommonAncestor(Node* p, Node * q) {
  Node* a = p;
  Node* b = q;
  while(a != b) {
    a = a == nullptr ? q : a->parent;
    b = b == nullptr ? p : b->parent;  
  }
  return a;
}

// 1123. Lowest Common Ancestor of Deepest Leaves
pair<TreeNode*, int>  GetDeepestLeaves(TreeNode* root, const int depth) {
  if (!root) return {root, -1};
  if (!root->left && !root->right) return {root, depth};
  const auto left = GetDeepestLeaves(root->left, depth + 1);
  const auto right = GetDeepestLeaves(root->right, depth + 1);
  if (left.second != right.second) return left.second > right.second? 
         left:right;
  else return {root, left.second};
} 

// 1676. Lowest Common Ancestor of a Binary Tree IV
TreeNode* LowestCommonAncestor(TreeNode* root, unordered_set<TreeNode*>& record) {
  if (!root || record.count(root) > 0) return root;
  const auto left = LowestCommonAncestor(root->left, record);
  const auto right = LowestCommonAncestor(root->right, record);
  return left && right?root: (left?left:right);
}
```

- Completeness of a Binary Tree

```c++
// 222. Count Complete Tree Nodes
int countNodes(TreeNode* root) {
  if (!root) return 0;
  auto left = root->left;
  int depth_left = 0;
  while (left) {
    ++depth_left;
    left = left->left;
  }
  auto right = root->right;
  int depth_right = 0;
  while (right) {
    ++depth_right;
    right = right->right;
  }
  return depth_left == depth_right ? pow(2, depth_left + 1) - 1:
     countNodes(root->left)  + countNodes(root->right) + 1;
}

// 958. Check Completeness of a Binary Tree
// Method 1: BFS
bool isCompleteTree(TreeNode* root) {
  queue<TreeNode*> record;
  if (root)
    record.push(root);
  bool prev = false;
  while (!record.empty()) {
    int size = record.size();
    for (int i = 0; i < size; ++i) {
      auto tp = record.front();
      record.pop();
      if (!tp) {
        prev = true;
      } else {
        if (prev) return false;
        record.push(tp->left);
        record.push(tp->right);
      }
    }
  }
  return true;
}

// Method 2: calculate the potential nodes number, then verify
pair<int, bool> CountNodes(TreeNode* root) {
  if (!root) return {0, true};
  auto left = root->left;
  int depth_left = 0;
  while (left) {
    ++depth_left;
    left = left->left;
  }
  auto right = root->right;
  int depth_right = 0;
  while (right) {
    ++depth_right;
    right = right->right;
  }
  if (depth_left == depth_right) {
    return {pow(2, depth_left + 1) -1, true};
  } else {
    if (depth_left < depth_right || depth_left > depth_right + 1) return {0, false};
    
    auto left = CountNodes(root->left);
    auto right = CountNodes(root->right);
    if (!left.second || !right.second || left.first < right.first) 
      return {0, false};
    else 
      return {left.first + right.first + 1, true};
  }
}  

bool DFS(TreeNode* node, int idx, vector<bool>& record) {
  if (!node) return true;
  if (idx > record.size()) return false;
  record[idx] = true;
  auto left = DFS(node->left, 2*idx, record);
  auto right = DFS(node->right, 2*idx+1, record);
  return left && right;
}  
  
bool isCompleteTree(TreeNode* root) {
  const auto [count, status] = CountNodes(root);
  if (!status) return false;
  vector<bool> record(count + 1, false);
  
  auto s = DFS(root, 1, record);
  if (!s) return false;
  for (int i = 1; i <= count; ++i)
    if (!record[i]) return false; 
  return true;
}
```

- Serialize and Deserialize Binary Tree

```c++
// 297. Serialize and Deserialize Binary Tree
string serialize(TreeNode* root) {
  queue<TreeNode*> que({root});
  string str;
  while (!que.empty()) {
    int size = que.size();
    for (int i = 0; i < size; ++i) {
      const auto tp = que.front();
      que.pop();
      if (tp) {
        que.push(tp->left);
        que.push(tp->right);
        str += to_string(tp->val) + ",";  
      } else {
        str += "n,";
      }
    }
  }
  return str;
}

// Decodes your encoded data to tree.
TreeNode* deserialize(string data) {
  if (data.empty()) return nullptr;
  
  TreeNode* root = nullptr;
  queue<TreeNode*> que;
  int cur = 0;
  int count = 0;
  bool neg = false;
  for (int i = 0; i < data.size(); ++i) {
    if (data[i] == ',') {
      TreeNode* tmp = cur < -1000? nullptr:new TreeNode(neg?-cur:cur);
      if (!root) {
        root = tmp;
      } else {
        while (!que.empty()) {
          auto tp = que.front();
          if (!tp) {
            que.pop();
            continue;
          }
          if (!count) tp->left = tmp;
          else tp->right = tmp;
          ++count;
          if (count == 2) {
            count = 0;
          que.pop();
        }
        break;
        }
      }
      que.push(tmp);
      cur = 0;
      neg = false;
    } else if (data[i] == 'n') {
      cur = -1001;
    } else if (data[i] == '-') {
      neg = true;
    } 
    else {
      cur = cur*10 + data[i] - '0';
    }
  }
  
  return root;
}
```

- binary search with k times add +1

- shortest distance to target character

```c++
vector<int> shortestToChar(string s, char c) {
  vector<int> ans(s.size(), INT_MAX);
  for (int i = 0; i < s.size(); ++i) {
    if (s[i] == c) {
      ans[i] = 0;
    }
  }
  for (int i = 0; i < s.size(); ++i) {
    if (s[i] == c) {
      int left = 1;
      while (i - left >= 0 && ans[i-left] > left) {
        ans[i-left] = left;
        ++left;
      }
      int right = 1;
      while (i + right < s.size() && ans[i + right] > right) {
        ans[i+right] = right;
        ++right;
      }
    }
  }
  return ans;
}
```

