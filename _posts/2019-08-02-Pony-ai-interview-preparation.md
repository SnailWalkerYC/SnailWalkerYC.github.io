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
vector<int> lexicalOrder(int n) {
  vector<int> ans;
  
  Helper(1, n, ans);
  
  return ans;
}
void Helper(const int stt, const int end, vector<int>& ans) {
  if (stt > end) return;
  ans.push_back(stt);
  Helper(stt * 10, end, ans);
  if (stt % 10 != 9) {
    Helper(stt + 1, end, ans);
  }
}  
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

- binary search/two pointers with k times add +1

```c++
// 1838. Frequency of the Most Frequent Element
int maxFrequency(vector<int>& nums, int k) {
  sort(begin(nums), end(nums));
  vector<long long> acc_sum(nums.size()+1, 0);
  for (int i = 0; i < nums.size(); ++i)
    acc_sum[i+1] = acc_sum[i] + nums[i];
  int max_res = 0;
  for (int i = 1, j = 1; i < acc_sum.size(); ++i) {
    while ((i - j + 1ll)*nums[i-1] - (acc_sum[i] - acc_sum[j-1]) > k) ++j;
    max_res = max(max_res, i - j + 1);
  }
  return max_res;
}
```

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

- Skyline 

```c++
// 218. The Skyline Problem
vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
  vector<pair<int, int>> pts;
  for (const auto& building : buildings) {
    pts.emplace_back(building[0], -building[2]);
    pts.emplace_back(building[1], building[2]);
  }
  sort(begin(pts), end(pts));
  multiset<int> pq({0});
  int prev_max = 0;
  vector<vector<int>> ans;
  for (const auto& pt : pts) {
    if (pt.second < 0) pq.insert(-pt.second);
    else pq.erase(pq.find(pt.second));
    const auto cur_max = *crbegin(pq);
    if (cur_max != prev_max) {
      ans.emplace_back(vector<int>({pt.first, cur_max}));
      prev_max = cur_max;
    }
  }
  return ans;
}
```

https://leetcode-cn.com/problems/the-skyline-problem/solution/218tian-ji-xian-wen-ti-sao-miao-xian-fa-by-ivan_al/ 

- Two Sum BST

```c++
// 653. Two Sum IV - Input is a BST
bool Find(TreeNode* root, unordered_set<int>& set1, const int k) {
  if (!root) return false;
  if (set1.count(k - root->val) > 0) return true;
  set1.insert(root->val);
  return Find(root->left, set1, k) || Find(root->right, set1, k);
}  
bool findTarget(TreeNode* root, int k) {
  unordered_set<int> set1;
  return Find(root, set1, k);
}

// 1214. Two Sum BSTs
void Traverse(TreeNode* root, unordered_set<int>& set1) {
  if (!root) return;
  set1.insert(root->val);
  Traverse(root->left, set1);
  Traverse(root->right, set1);
}  

bool Find(TreeNode* root, unordered_set<int>& set1, const int target) {
  if (!root) return false;
  if (set1.count(target - root->val) > 0) return true;
  return Find(root->left, set1, target) || Find(root->right, set1, target);
}
  
bool twoSumBSTs(TreeNode* root1, TreeNode* root2, int target) {
  unordered_set<int> set1;
  Traverse(root1, set1);
  return Find(root2, set1, target);
}
```

- Basic Calculator

```c++
// 772. Basic Calculator III
string parserString (string s, int idx, int & new_idx) {
  int num_brance = 1;
  string str = ""; 
  for (int i = idx; i < s.size(); ++i) {
    if (s[i] == '(') {
      ++num_brance;
    }
    else if (s[i] == ')') {
      --num_brance;
    }        
    if (!num_brance) {
      new_idx = i;
      return str;
    }        
    str += s[i];
  }      
  return str;
}
int calculate(string s) {
  int s_len = s.size();
  long long cur_num = 0;
  int cur_res = 0;
  int fin_res = 0;
  char ope = '+';      
  for (int idx = 0; idx < s_len; ++idx) {
    if (s[idx] == '(') {
      int new_idx = 0;
      string str = parserString(s, idx+1, new_idx);
      cur_num = calculate(str);
      idx = new_idx;
    }        
    if (isdigit(s[idx])) {
      cur_num = cur_num*10 + (s[idx] - '0');
    }        
    if (s[idx] == '+' || s[idx] == '-' || s[idx] == '*' ||
      s[idx] == '/' || s_len == idx + 1) {
      switch(ope) {
        case '+': cur_res += cur_num; break;
        case '-': cur_res -= cur_num; break;
        case '*': cur_res *= cur_num; break;
        case '/': cur_res /= cur_num; break;    
      }
      if (s[idx] == '-' || s[idx] == '+' || s_len - 1 == idx) {
        fin_res += cur_res;
        cur_res = 0;
      }        
      ope = s[idx];
      cur_num = 0;
    }
  }      
  return fin_res;
}
```



Fractional class：https://www.cnblogs.com/gongkai/p/10806080.html

```c++
friend ostream& operator<< (ostream& os,const fraction& the_fraction);
friend istream& operator>> (istream& is,fraction& the_fraction);     
friend fraction operator+  (const fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator-  (const fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator*  (const fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator/  (const fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator+= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator-= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator*= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator/= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend fraction operator-  (const fraction& the_fraction);
friend fraction operator++ (fraction& the_fraction); 
friend fraction operator++ (fraction& the_fraction,int);
friend fraction operator-- (fraction& the_fraction);
friend fraction operator-- (fraction& the_fraction,int);
    
friend bool operator>  (fraction& the_fraction_a,const fraction& the_fraction_b);
friend bool operator<  (fraction& the_fraction_a,const fraction& the_fraction_b);
friend bool operator>= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend bool operator<= (fraction& the_fraction_a,const fraction& the_fraction_b);
friend bool operator== (fraction& the_fraction_a,const fraction& the_fraction_b);
friend bool operator!= (fraction& the_fraction_a,const fraction& the_fraction_b);
      
fraction();
fraction(long long the_numerator);
fraction(long long the_numerator,long long the_denominator);
fraction(fraction the_fraction_a,fraction the_fraction_b);
```

