---
layout: post
title: Pony ai interview preparation
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false
---



```c++
// CUDA: max pooling; convolution; average pooling.
// See the CUDA preparation part.

// BFS 变形题. One exit & calculate the shortest time. 从离开的点出发即可。matrix里面有很多人和一个出口，问离出口最远的人的距离

// 274. H-Index
int hIndex(vector<int>& citations) {
  sort(begin(citations), end(citations), greater<int>());
  for (int i = 0; i < citations.size(); ++i) {
    if (citations[i] < i + 1) return i;
  }
  return citations.size();
}

// skyline 变形
vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
  vector<vector<int>> pts;
  for (const auto& pt: buildings) {
    pts.push_back({pt[0], -pt[2]});
    pts.push_back({pt[1], pt[2]});
  }
  sort(begin(pts), end(pts));
  multiset<int> m({0});
  int prev_max = 0;
  vector<vector<int>> ans;
  for (const auto& pt : pts) {
    if (pt[1] < 0) m.insert(-pt[1]);
    else m.erase(m.find(pt[1]));
    const auto cur_max = *crbegin(m);
    if (cur_max != prev_max) {
      ans.push_back({pt[0], cur_max});
      prev_max = cur_max;
    }
  }
  return ans;
}

// Dijkstra
// http://yuchenspace.info/graph-shortest-path/

// HashMap, design  set, get, setAll(value), by vector.

// 给一个有向图，判断否形成二叉树; 判断图是否是树，要有一些early exit。图的遍历，是否有环，以及child。给了一堆含有起点和终点的pair，问这些pair是否能够构成二叉树。
// 如何判断一个点是否在三角形内。面积，角度。
// 多线程角度：pip、FIFO、message queue、shared memory、socket、signal

// 23. Merge k Sorted Lists
ListNode* MergeLists(vector<ListNode*>& lists, const int stt, const int fin) {
  if (stt == fin) return lists[stt];
  if (stt > fin) return nullptr;
  ListNode* left = MergeLists(lists, stt, stt + (fin - stt) / 2);
  ListNode* right = MergeLists(lists, stt + (fin - stt) / 2 + 1, fin);
  ListNode* root = new ListNode(0);
  ListNode* cur = root;
  while (left && right) {
    if (left->val <= right->val) {
      cur->next = left;
      left = left->next;
    } else {
      cur->next = right;
      right = right->next;
    }
    cur = cur->next;
  }
  if (left) {
    cur->next = left;
  }
  if (right) {
    cur->next = right;
  }
  return root->next;
}  
  
ListNode* mergeKLists(vector<ListNode*>& lists) {
  return MergeLists(lists, 0, lists.size()-1);        
}

// 给一棵树，List of Node，寻找整个List Node的LCA
// 可以返回在当前树下的节点
TreeNode* lowestCommonAncestor(TreeNode* root, 
                                   TreeNode* p, TreeNode* q) {
  if (!root || p == root || q == root) return root;
  const auto left = lowestCommonAncestor(root->left, p, q);
  const auto right = lowestCommonAncestor(root->right, p, q);
  
  return left && right? root:(left?left:right);
}



class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        UnionFindSet s(edges.size());
        for(const auto& edge: edges) {
            if(!s.Union(edge[0],edge[1])) {
                return edge;
            }
        }    
        return {};
    }
};
  
// 435. Non-overlapping Intervals
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
  if (intervals.empty()) return 0;
  sort(begin(intervals), end(intervals), [](const vector<int>& v1,
                                            const vector<int>& v2) {
    return v1[1] < v2[1];
  });       
  int res = 0;
  int ed = -2e9;
  for (const auto& inter : intervals) {
    if (ed <= inter[0]) {
      ++res;
      ed = inter[1];
    }
  }
  return intervals.size() - res;
}

// 给一些一维区间，问最多能选几个区间，要求选出的区间中任意两个不能有重合。
int MaxNonOverlap(vector<pair<int, int>>& nums) {
  sort(begin(nums), end(nums), [](const pair<int, int>& n1, 
                                 const pair<int, int>& n2) {
    return n1.second < n2.second;
  });
  int res = 0;
  int ed = -2e9;
  for (int i = 0; i < nums.size(); ++i) {
    if (ed < nums[i].first) {
      ++res;
      ed = nums[i].second;
    }
  }
}

// 684. Redundant Connection
// Union-find the find this redundant connection.
class UnionFindSet {
 public:
  vector<int> parents_;
  vector<int> ranks_;
  UnionFindSet(int n) {
    ranks_=vector<int>(n+1,0);
    parents_=vector<int>(n+1,0);
    for(int i=0;i<parents_.size();i++) {
      parents_[i]=i;
    }
  }
  bool Union(int u, int v) {
    int pu=Find(u);
    int pv=Find(v);
    if(pu==pv) {
      return false;
    }
    if(ranks_[pv]>ranks_[pu]){
      parents_[pu]=pv;
    }
    else if(ranks_[pu]>ranks_[pv]){
      parents_[pv]=pu;
    }
    else{
      parents_[pv]=pu;
      ranks_[pv]+=1;
    } 
    return true;
  }
  int Find(int u){
    if(u!=parents_[u]){
      parents_[u]=Find(parents_[u]);
    }
    return parents_[u];
  }    
};    
  
// 85. Maximal Rectangle
int maximalRectangle(vector<vector<char>>& matrix) {
  if (matrix.empty() || matrix[0].empty()) return 0;
  vector<int> heights(matrix[0].size() + 2, 0);
  int max_area = 0;
  for (int i = 0; i < matrix.size(); ++i) {
    for (int j = 0; j < matrix[0].size(); ++j) {
      if (matrix[i][j] == '0') {
        heights[j+1] = 0;
      } else {
        ++heights[j+1];
      }
    }
    max_area = max(max_area, MaxHistogramArea(heights));
  }
  return max_area;
}  
int MaxHistogramArea(const vector<int>& heights) {
  stack<int> record;
  record.push(heights[0]);
  int max_area = 0;
  for (int i = 1; i < heights.size(); ++i) {
    while (heights[record.top()] > heights[i]) {
      const int mid = record.top();
      record.pop();
      max_area = max(max_area, heights[mid] * (i - record.top() - 1));
    }
    record.push(i);
  }
  return max_area;
}  

// 221. Maximal Square
// 0101的二维数组，0代表空地，1代表地雷，找出有多少个d*d的正方形是没有地雷的
// DP method
int maximalSquare(vector<vector<char>>& matrix) {
  if (!matrix.size() || !matrix[0].size()) {
    return 0;
  }      
  int row = matrix.size();
  int col = matrix[0].size();
  int maxLen = 0;
  vector<vector<int>> rec(row, vector<int>(col, 0));     
  for (int idx = 0; idx < row; ++idx) {
    maxLen = max(maxLen, matrix[idx][0] - '0');   
    rec[idx][0] = matrix[idx][0] - '0';
  }
  for (int idx = 0; idx < col; ++idx) {
    maxLen = max(maxLen, matrix[0][idx] - '0');
    rec[0][idx] = matrix[0][idx] - '0';
  }    
  for (int r = 1; r < row; ++r) {
    for (int c = 1; c < col; ++c) {
      if (matrix[r][c] == '1') {
        rec[r][c] = min( min(rec[r-1][c], rec[r][c-1]), rec[r-1][c-1]) + 1;
        maxLen = max(maxLen, rec[r][c]);
      }
    }
  }
  return maxLen*maxLen;
}
// Accumulate sum method
void accumulate_volumn(vector<vector<int>>& rec, vector<vector<char>>& matrix, int m, int n) {
  for(int col=1; col<=n; col++) {
    for(int row = 1; row<=m; row++) {
      rec[row][col] = rec[row-1][col]+matrix[row-1][col-1]-'0';
    }    
  }    
}        
void accumulate_row(vector<vector<int>>& rec, vector<vector<char>>& matrix, int m, int n) {
  for(int row=1; row<=m; row++) {
    for(int col = 1; col<=n; col++) {
      rec[row][col] += rec[row][col-1];
    }    
  }    
}     
bool isFullSquare(vector<vector<int>>& rec, int row, int col, int size) {
  int target = size*size;
  int cur = rec[row+size-1][col+size-1]+rec[row-1][col-1]-rec[row-1][col+size-1]-rec[row+size-1][col-1];
  return cur==target;
}    
int maximalSquare(vector<vector<char>>& matrix) {
  int m=matrix.size();
  int n=m?matrix[0].size():0;
  vector<vector<int>> rec(m+1, vector<int>(n+1,0));
  accumulate_volumn(rec, matrix, m, n);
  accumulate_row(rec, matrix, m, n);
  int ans = 0;
  int size = min(m, n);
  while(size>=1) {
    for(int row=1; row<=m-size+1; row++) {
      for(int col=1; col<=n-size+1;col++) {
        if(isFullSquare(rec, row, col, size))
          return size*size;
      }    
    }    
    size--;
  }    
  return 0;
}
  
// 543. Diameter of Binary Tree
// DFS
int diameterOfBinaryTree(TreeNode* root, int& ans) {
  if(!root)
    return -1;
  int left = diameterOfBinaryTree(root->left, ans);
  int right = diameterOfBinaryTree(root->right, ans);
  ans = max(ans, left+right+2);
  return max(left,right)+1;
}    
int diameterOfBinaryTree(TreeNode* root) {
  int ans=0;
  diameterOfBinaryTree(root, ans);
  return ans;
}
// BFS: construct graph
unordered_map<int, unordered_set<int>> graph;
int max_depth_ = 0;
int node_ = -1;
int count = 0;  
void BFS(TreeNode* root, const int cur_depth, const int cur_val) {
  if (!root) return;
  if (cur_depth > max_depth_) {
    node_ = root->val;
    max_depth_ = cur_depth;
  }
  if (root->left) {
    root->left->val = count++;
    graph[root->val].insert(root->left->val);
    graph[root->left->val].insert(root->val);
    BFS(root->left, cur_depth + 1, root->left->val);
  }
  if (root->right) {
    root->right->val = count++;
    graph[root->val].insert(root->right->val);
    graph[root->right->val].insert(root->val);
    BFS(root->right, cur_depth + 1, root->right->val);
  }  
}  
int GetLongestPath(const int n, unordered_map<int, unordered_set<int>>& graph) {
  int max_len = 0;
  if (!graph.count(n)) return 0;
  for (const auto v : graph.at(n)) {
    graph[v].erase(n);
    max_len = max(GetLongestPath(v, graph), max_len);
  }
  graph.erase(n);
  return max_len + 1;
}  
int diameterOfBinaryTree(TreeNode* root) {
  if (!root) return 0;
  root->val = count++;
  BFS(root, 0, 1);
  return max(0, GetLongestPath(node_, graph) - 1);
}

// 1522. n-array tree for diameter
int Diameter(Node* root, int& ans) {
  if (!root) return -1;
  list<int> record;
  for (const auto n : root->children) {
    const int len = Diameter(n, ans);
    if (record.size() < 2)
      record.push_back(len);
    else if (record.front() < len || record.back() < len) {
      if (record.front() > record.back()) 
        record.pop_back();
      else record.pop_front();
      record.push_back(len);
    }
  }
  int cur1 = record.empty() ? -1:record.front();
  if (!record.empty()) record.pop_front();
  int cur2 = record.empty() ? -1: record.back();
  ans = max(ans, cur1 + cur2 + 2);
  return max(cur1, cur2) + 1;
}  
int diameter(Node* root) {
  if (!root) return 0;
  int ans = 0;
  Diameter(root, ans);
  return ans;
}

// 792. Number of Matching Subsequences
int addOne(string A, string B) {
  int idx=0;
  int idx2=0;
  while(idx<A.size() && idx2<B.size()) {
    if(B[idx2]==A[idx]) { 
      idx2++;
      idx++;
      continue;
    }    
    idx++;
  }    
  return idx2==B.size();
}    
bool mapContain(unordered_map<char,int> A, string B) {
  for(auto c:B) {
    A[c]--;
    if(A[c]<0)
      return false;
  }    
  return true;
}    
int numMatchingSubseq(string S, vector<string>& words) {
  int len=words.size();
  vector<queue<string>> rec(26);
  for(auto w:words)
    rec[w[0]-'a'].push(w);
  int ans=0;
  for(auto c:S) {
    int len_q = rec[c-'a'].size();
    for(int i=0;i<len_q;i++){
      string tp=rec[c-'a'].front().substr(1);
      rec[c-'a'].pop();
      if(!tp.size())
        ans++;
      else
        rec[tp[0]-'a'].push(tp);
    }    
  }    
  return ans;
}

// 489. Robot Room Cleaner

// 33. Search in Rotated Sorted Array
// Follow up, with duplicate

// Buy stock series.
// 121. Best Time to Buy and Sell Stock
int maxProfit(vector<int>& prices) {
  int buy = prices[0];
  int p = 0;
  for (int i = 1; i < prices.size(); ++i) {
    if (prices[i] > buy) {
      p = max(p, prices[i] - buy);
    } else {
      buy = prices[i];
    }
  }
  return p;
}

// 122. Best Time to Buy and Sell Stock II
// 123. Best Time to Buy and Sell Stock III
// 188. Best Time to Buy and Sell Stock IV
// 309. Best Time to Buy and Sell Stock with Cooldown
// 714. Best Time to Buy and Sell Stock with Transaction Fee

// 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

// 386. Lexicographical Numbers

给了一堆点的横纵坐标，要求找到一个三角形，这个三角形不包含任何其他的点

// Candidate: 786, 291, 1235, 1824, 1838.  
```



