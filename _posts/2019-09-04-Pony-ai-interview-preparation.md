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

// 33. Search in Rotated Sorted Array
// Follow up, with duplicate
int search(vector<int>& nums, int target) {
  if (!nums.size()) return -1;
  int stt_idx = 0;
  int fin_idx = nums.size() - 1;
  while (stt_idx + 1 < fin_idx) {
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2;
    const int val = nums[mid_idx];
    if (nums[stt_idx] < val) {
      if (val >= target && nums[stt_idx] <= target) fin_idx = mid_idx;
      else stt_idx = mid_idx;
    } else {
      if (val <= target && nums[fin_idx] >= target) stt_idx = mid_idx;
      else fin_idx = mid_idx;
    }
  }
  if (nums[stt_idx] == target) return stt_idx;
  if (nums[fin_idx] == target) return fin_idx;
  return -1;
}

bool search(vector<int>& nums, int target) {
  if (!nums.size()) return false;
  int stt_idx = 0;
  int fin_idx = nums.size() - 1;
  while (stt_idx + 1 < fin_idx) {
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2;
    const int val = nums[mid_idx];
    if (nums[stt_idx] < val) {
      if (val >= target && nums[stt_idx] <= target) fin_idx = mid_idx;
      else stt_idx = mid_idx;
    } else if (nums[stt_idx] > val){
      if (val <= target && nums[fin_idx] >= target) stt_idx = mid_idx;
      else fin_idx = mid_idx;
    } else {
      ++stt_idx;
    }       
  }
  if (nums[stt_idx] == target || nums[fin_idx] == target) return true;
  return false;  
}

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
  
// 1824. Minimum Sideway Jumps 
class Solution {
public:
  struct Node {
    int dis_ = INT_MAX;
    int lane_ = -1;
    int point_ = -1;
    Node(const int lane, const int point, const int dis = INT_MAX) {
      lane_ = lane;
      point_ = point;
      dis_ = dis;
    }
  };
  struct Comp {
    bool operator()(const Node& node1, const Node& node2) {
      return node1.dis_ > node2.dis_;
    }
  };
  pair<int, int> OtherLane(const int num) {
    if (num == 1) return make_pair(2, 3);
    if (num == 2) return make_pair(1, 3);
    return make_pair(1, 2);
  }
  int minSideJumps(vector<int>& obstacles) {
    const int n = obstacles.size() - 1;
    priority_queue<Node, vector<Node>, Comp> pq;
    vector<vector<int>> history(4, vector<int>(obstacles.size(), INT_MAX));
    pq.push(Node(2, 0, 0));
    pq.push(Node(1, 0, 1));
    pq.push(Node(3, 0, 1));
    history[2][0] = 0;
    history[1][0] = 1;
    history[3][0] = 1;
    while (!pq.empty()) {
      const auto tp = pq.top();
      pq.pop();
      const auto [l1, l2] = OtherLane(tp.lane_);
      if (obstacles[tp.point_] != l1) {
        if (history[l1][tp.point_] > tp.dis_ + 1) {
          history[l1][tp.point_] = tp.dis_ + 1;
          pq.push(Node(l1, tp.point_, tp.dis_ + 1));
        }
      }
      if (obstacles[tp.point_] != l2) {
        if (history[l2][tp.point_] > tp.dis_ + 1) {
          history[l2][tp.point_] = tp.dis_ + 1;
          pq.push(Node(l2, tp.point_, tp.dis_ + 1));
        }
      }
      if (tp.point_ < n && obstacles[tp.point_+1] != tp.lane_) {
        history[tp.lane_][tp.point_+1] = tp.dis_;
        pq.push(Node(tp.lane_, tp.point_ + 1, tp.dis_));
      }
    }
    return min(min(history[1][n], history[2][n]), history[3][n]);
  }
};
  
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
// Clean solution
int MaxProfit(const vector<int>& nums) {
  int profit = 0;
  for (int i = 0, min_p = INT_MAX; i < nums.size(); ++i) {
    profit = max(profit, nums[i] - min_p);
    min_p = min(nums[i], min_p);
  }
  return profit;
}

// 122. Best Time to Buy and Sell Stock II
int maxProfit(vector<int>& prices) {
  int profit = 0;
  for (int i = 0; i + 1< prices.size(); ++i) {
    profit += max(0, prices[i+1] - prices[i]);
  }
  return profit;
}

// 123. Best Time to Buy and Sell Stock III
// f(i) = p(i) - min_p
//      = f(i-1)
// 前后缀分解
int maxProfit(vector<int>& prices) {
  vector<int> f(prices.size()+2, 0);
  for (int i = 1, min_p = INT_MAX; i <= prices.size(); ++i) {
    f[i] = max(f[i-1], prices[i-1]-min_p);
    min_p = min(min_p, prices[i-1]);
  }
  int res = 0;
  for (int i = prices.size(), max_p = 0;
       i >= 1; --i) {
    res = max(res, f[i-1] + max_p - prices[i-1]);
    max_p = max(prices[i-1], max_p);
  }
  return res;
}

// 188. Best Time to Buy and Sell Stock IV
// state machine DP
// 0(no buy stock)  1(buy stock)
int maxProfit(int k, vector<int>& prices) {
  const int INF = -1e8;
  const int size = prices.size();
  if (k >= size/2) {
    int res = 0;
    for (int i = 1; i < prices.size(); ++i) {
      res += max(0, prices[i] - prices[i-1]);
    }
    return res;
  }
  vector<vector<int>> f(size+1, vector<int>(k+1, INF));
  auto g = f;
  int res = 0;
  f[0][0] = 0;
  for (int i = 1; i <= size; ++i) {
    for (int j = 0; j <= k; ++j) {
      f[i][j] = max(f[i-1][j], g[i-1][j] + prices[i-1]);
      g[i][j] = g[i-1][j];
      if (j) g[i][j] = max(g[i][j], f[i-1][j-1] - prices[i-1]);
      res = max(res, f[i][j]);
    }
  }
  return res;
}
// Optimization
int maxProfit(int k, vector<int>& prices) {
  const int INF = -1e8;
  const int size = prices.size();
  if (k >= size/2) {
    int res = 0;
    for (int i = 1; i < prices.size(); ++i) {
      res += max(0, prices[i] - prices[i-1]);
    }
    return res;
  }
  vector<vector<int>> f(2, vector<int>(k+1, INF));
  auto g = f;
  int res = 0;
  f[0][0] = 0;
  for (int i = 1; i <= size; ++i) {
    for (int j = 0; j <= k; ++j) {
      f[i & 1][j] = max(f[i-1 & 1][j], g[i-1 & 1][j] + prices[i-1]);
      g[i & 1][j] = g[i-1 & 1][j];
      if (j) g[i & 1][j] = max(g[i & 1][j], f[i-1 & 1][j-1] - prices[i-1]);
      res = max(res, f[i & 1][j]);
    }
  }
  return res;
}

// 714. Best Time to Buy and Sell Stock with Transaction Fee
// https://www.acwing.com/problem/content/description/1165/
int maxProfit(vector<int>& prices, int fee) {
  const int size = prices.size();
  const int INF = 1e8;
  vector<vector<int>> f(size+1, vector<int>(2, -INF));
  f[0][0] = 0;
  int res = 0;
  for (int i = 1; i <= size; ++i) {
    f[i][0] = max(f[i-1][0], f[i-1][1] + prices[i-1]);
    f[i][1] = max(f[i-1][1], f[i-1][0] - prices[i-1] - fee);
    res = max(res, f[i][0]);
  }
  return res;
}

// 309. Best Time to Buy and Sell Stock with Cooldown

// 1923. Longest Common Subpath

// 1235. Maximum Profit in Job Scheduling

// 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

// 1239. Maximum Length of a Concatenated String with Unique Characters

// 1916. Count Ways to Build Rooms in an Ant Colony

// 786. K-th Smallest Prime Fraction


// 1922. Count Good Numbers
long long MOD = 1e9 + 7;  
long long Quick(long long num, long long t) {
  long long res = 1;
  while (t) {
    if (t & 1) res = res * num % MOD;
    num = num * num % MOD;
    t >>= 1;
  }
  return res;
}  
int countGoodNumbers(long long n) {
  return Quick(5, (n+1)/2) * Quick(4, n/2) % MOD;
}

// 1915. Number of Wonderful Substrings
int s[100001][10] = {0};
int cnt[1024] = {0};  
long long wonderfulSubstrings(string str) {
  ++cnt[0];
  long long res = 0;
  for (int i = 1; i <= str.size(); ++i) {
    for (int j = 0; j < 10; ++j) {
      if (str[i-1] - 'a' == j) {
        s[i][j] = s[i-1][j] + 1;
      } else {
        s[i][j] = s[i-1][j];
      }
    }
    int state = 0;
    for (int j = 0; j < 10; ++j) {
      state = state * 2 + s[i][j] % 2;
    }
    res += cnt[state];
    for (int j = 0; j < 10; ++j) {
      res += cnt[state ^ (1 << j)];
    }
    ++cnt[state];
  }
  return res;
}

// 291. Word Pattern II
bool wordPatternMatch(string pattern, string s) {
  unordered_map<char, string> mp;
  unordered_set<string> in_mp;
  return Pattern(pattern, 0, s, 0, mp, in_mp);      
}
bool Pattern(const string& p, const int p_idx, 
             const string& s, const int s_idx, 
             unordered_map<char, string>& mp,
             unordered_set<string>& in_mp) {
  if (p_idx == p.size() && s.size() == s_idx) {
    return true;
  } else if (p_idx == p.size() || s.size() == s_idx) {
    return false;
  }
  for (int i = s_idx; i < s.size(); ++i) {
    const string cur = s.substr(s_idx, i - s_idx + 1);
    if (mp.count(p[p_idx]) > 0) {
      if (mp[p[p_idx]] != cur) continue;
      if (Pattern(p, p_idx+1, s, i+1, mp, in_mp)) {
        return true;
      }
    } else {
      if (in_mp.count(cur) > 0) continue;
      mp[p[p_idx]] = cur;
      in_mp.insert(cur);
      if (Pattern(p, p_idx+1, s, i+1, mp, in_mp)) {
        return true;
      }
      mp.erase(p[p_idx]);
      in_mp.erase(cur);
    }
  }
  return false;
}  

// 1349. Maximum Students Taking Exam
// TLE
const int dirs_[2][4] = { {-1, 1, -1, 1}, {0, 0, -1, -1} };  
int cur_ = 0;  
void MaxStudents(int x, int y, const int cur, 
                vector<vector<char>>& seats) {
  if (x >= seats[0].size()) {
    x = 0;
    ++y;
  }
  if (y >= seats.size()) {
    cur_ = max(cur, cur_);
    return;
  }
  while (seats[y][x] == '#') {
    ++x;
    if (x >= seats[0].size()) {
      x = 0;
      ++y;
    }
    if (y >= seats.size()) {
      cur_ = max(cur, cur_);
      return;
    }
  }
  bool is_ok = true;
  for (int i = 0; i < 4; ++i) {
    const int new_x = dirs_[0][i] + x;
    const int new_y = dirs_[1][i] + y;
    if (new_x < 0 || new_x >= seats[0].size() ||
        new_y < 0 || new_y >= seats.size()) {
      continue;
    }
    if (seats[new_y][new_x] == '+') {
      is_ok = false;
      break;
    }
  }  
  if (is_ok) {
    seats[y][x] = '+';
    MaxStudents(x+1, y, cur+1, seats);
    seats[y][x] = '.';
    MaxStudents(x+1, y, cur, seats);
  } else {
    MaxStudents(x+1, y, cur, seats);
  }
}  
int maxStudents(vector<vector<char>>& seats) {
  MaxStudents(0, 0, 0, seats);        
  return cur_;
}

// 1525. Number of Good Ways to Split a String
int numSplits(string s) {
  int left[26] = {0};
  int right[26] = {0};
  int num_left = 1;
  int num_right = 0;
  ++left[s[0]-'a'];
  for (int i = 1; i < s.size(); ++i) {
    ++right[s[i]-'a'];
    if (right[s[i]-'a'] == 1)
      ++num_right;
  }
  int res = num_left == num_right;
  for (int i = 1; i < s.size() - 1; ++i) {
    --right[s[i]-'a'];
    if (!right[s[i]-'a'])
      --num_right;
    ++left[s[i]-'a'];
    if (left[s[i]-'a'] == 1)
      ++num_left;
    res += num_left == num_right;
  }
  return res;
}

// 489. Robot Room Cleaner
unordered_set<string> record_;
const int dirs_[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };  
void cleanRoom(Robot& robot, const int x = 0, 
               const int y = 0, const int dir = 0) {
  const auto key = to_string(x) + " " + to_string(y);
  if (record_.count(key)) return;
  record_.insert(key);
  robot.clean();
  for (int i = 0; i < 4; ++i) {
    const int new_x = x + dirs_[0][(i+dir)%4];
    const int new_y = y + dirs_[1][(i+dir)%4];
    if (robot.move()) {
      cleanRoom(robot, new_x, new_y, (dir + i)%4);
      robot.turnRight();
      robot.turnRight();
      robot.move();
      robot.turnRight();
      robot.turnRight();
    }
    robot.turnRight();
  }
}

// 给了一堆点的横纵坐标，要求找到一个三角形，这个三角形不包含任何其他的点
  
// 759. Employee Free Time
vector<Interval> MergeIntervals(const vector<vector<Interval>>& schedule, const 
                                int left, const int right) {
  if (left == right) return schedule[left];
  else if (left > right) return {};
  
  const int mid = left + (right - left)/2;
  const auto left_int = MergeIntervals(schedule, left, mid);
  const auto right_int = MergeIntervals(schedule, mid + 1, right);
  if (left_int.empty()) return right_int;
  if (right_int.empty()) return left_int;
  vector<Interval> ans;
  int stt = INT_MIN;
  int ed = INT_MIN;
  int idx1 = 0;
  int idx2 = 0;
  while (idx1 < left_int.size() && idx2 < right_int.size()) {
    if (ed < min(left_int[idx1].start, right_int[idx2].start)) {
      if (stt >= 0) {
        ans.emplace_back(Interval(stt, ed));
      } 
      stt = min(left_int[idx1].start, right_int[idx2].start);
      if (left_int[idx1].start >= right_int[idx2].start) {
        ed = right_int[idx2].end;
        ++idx2;
      }
      else { 
        ed = left_int[idx1].end;
        ++idx1;
      }
    } else {
      const int tmp = ed;
      if (ed >= left_int[idx1].start) {
        ed = max(ed, left_int[idx1].end);
        ++idx1;
      }
      if (tmp >= right_int[idx2].start) {
        ed = max(ed, right_int[idx2].end);
        ++idx2;
      }
    }
  }
  ans.emplace_back(Interval(stt, ed));
  while (idx1 < left_int.size()) {
    if (ans.back().end >= left_int[idx1].start) {
      ans.back().end = max(left_int[idx1].end, ans.back().end);
    } else {
      ans.emplace_back(left_int[idx1]);
    }
    ++idx1;
  }
  while (idx2 < right_int.size()) {
    if (ans.back().end >= right_int[idx2].start) {
      ans.back().end = max(right_int[idx2].end, ans.back().end);
    } else {
      ans.emplace_back(right_int[idx2]);
    }
    ++idx2;
  }
  return ans;
}  
vector<Interval> employeeFreeTime(vector<vector<Interval>> schedule) {
  vector<Interval> intervals = MergeIntervals(schedule, 0, schedule.size()-1);      
  vector<Interval> ans;
  int stt = INT_MIN;
  for (const auto& interval : intervals) {
    if (stt >= 0)
      ans.emplace_back(Interval(stt, interval.start));
    stt = interval.end;
  }
  return ans;
}  
```



