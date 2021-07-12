---
layout: post
title: Baidu interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



For the projects, see the DL/ML summary; CUDA summary; multi-threading.



```c++
// uni-value tree
// 965. Univalued Binary Tree
bool isUnivalTree(TreeNode* root, const int val = 100) {
  if (!root) return true;
  if (val != 100 && root->val != val) return false;
  return isUnivalTree(root->left, root->val) && isUnivalTree(root->right, root->val);
}

// 链表有随机节点的那个
// 138. Copy List with Random Pointer
Node* copyRandomList(Node* head) {
  unordered_map<Node*, Node*> record;
  record[nullptr] = nullptr;
  Node* new_head = new Node(0);
  auto copy_cur = new_head;
  auto cur = head;
  while (cur) {
    copy_cur->next = new Node(cur->val);
    copy_cur = copy_cur->next;
    record[cur] = copy_cur;
    cur = cur->next;
  }
  cur = head;
  copy_cur = new_head->next;
  while (cur) {
    copy_cur->random = record[cur->random];
    copy_cur = copy_cur->next;
    cur = cur->next;
  }
  return new_head->next;
}

// 问了如何check一个点在一个长方形内，给提供长方形四个点坐标
// 问了如何check两个长方形有相交
// https://blog.csdn.net/weixin_43619346/article/details/107513919

// 几何题给一个多段线段首尾相连组成的折线段，比如AB,BC,CD， 分割成等距离的k段，找到所有分段点的坐标。题目挺直白的，顺利做出来了。
// 向量，根据比例给值

// 168. Excel Sheet Column Title
string convertToTitle(int n) {
  string rel;
  while(n > 0) {
    int n1 = (n-1)/26;
    int n2 = (n-1)%26;
    n = n1;
    rel = char(n2 + 'A') + rel; 
  }      
  return rel;
}

// 八皇后
// 51. N-Queens
static constexpr int kNum = 10;
char kQueen[kNum][kNum];
bool row[kNum] = {false};
bool col[kNum] = {false};  
bool dg[2*kNum] = {false};
bool udg[2*kNum] = {false};
void StoreResult(const int N, vector<vector<string>>& ans) {
  vector<string> sol;
  for (int i = 0; i < N; ++i) {
    string tmp;
    for (int j = 0; j < N; ++j) {
      tmp += kQueen[i][j];
    }
    sol.push_back(tmp);
  }
  ans.push_back(sol);
}  
void DFS(int x, int y, 
         const int n, const int N,
        vector<vector<string>>& ans) {
  if (n > N) return;
  if (y == N) {
    y = 0;
    ++x;
  }
  if (x == N) {
    if (n == N) {
      StoreResult(N, ans);
    }
    return;
  }
  kQueen[x][y] = '.';
  DFS(x, y+1, n, N, ans);
  if (!row[y] && !col[x] && !dg[x+y] && !udg[-y+x+N]) {
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = true;
    kQueen[x][y] = 'Q';
    DFS(x, y + 1, n+1, N, ans);
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = false;
    kQueen[x][y] = '.';
  }
}  
vector<vector<string>> solveNQueens(int n) {
  vector<vector<string>> ans;
  DFS(0, 0, 0, n, ans);
  return ans;
}

// 719. Find K-th Smallest Pair Distance
// binary search + sliding window
int Check(const int num, const vector<int>& nums) {
  int res = 0;
  for (int i = 0, j = 0; i < nums.size(); ++i) {
    while (nums[i] - nums[j] > num)
      ++j;
    res += i - j;
  }
  return res;
}  
  
int smallestDistancePair(vector<int>& nums, int k) {
  sort(begin(nums), end(nums));
  int l = 0;
  int r = nums.back() - nums[0];
  while (l + 1 < r) {
    const int mid = l + (r - l)/2;
    if (Check(mid, nums) >= k) {
      r = mid;
    } else {
      l = mid;
    }
  }
  if (Check(l, nums) >= k) return l;
  return r;
}
// priority_queue, Time exceeded.
struct Compare {
  bool operator()(const vector<int>& v1, const vector<int>& v2) {
    return v1[0] > v2[0];
  }
};  
int smallestDistancePair(vector<int>& nums_init, int k) {
  map<int, int> record;
  for (const auto& num : nums_init)
    ++record[num];
  vector<int> nums;
  // diff, stt_idx, len
  priority_queue<vector<int>, vector<vector<int>>, Compare> pq;
  for (const auto& [k, count] : record) {
    nums.emplace_back(k);
  }    
  for (int i = 0; i < nums.size(); ++i) {
    if (record[nums[i]] > 1) {
      const auto count = record[nums[i]];
      pq.push({0, i, 0, count * (count - 1) / 2});
    } else if (i+1 < nums.size()) {
      pq.push({nums[i+1] - nums[i], i, 1, record[nums[i+1]]*record[nums[i]]});
    }
  }
  --k;
  while (k > 0) {
    const auto tp = pq.top();
    k -= tp[3];
    if (k < 0) {
      return tp[0];
    }
    pq.pop();
    if (tp[1]+tp[2]+1 < nums.size()) {
      pq.push({nums[tp[1]+tp[2]+1] - nums[tp[1]], tp[1], tp[2]+1,
               record[nums[tp[1]+tp[2]+1]] * record[nums[tp[1]]]});
    }
  }
  return pq.top()[0];
}

// 826. Most Profit Assigning Work
int maxProfitAssignment(vector<int>& difficulty, vector<int>& profit, vector<int>& worker) {
  vector<vector<int>> tasks;
  for (int i = 0; i < difficulty.size(); ++i) {
    tasks.emplace_back(vector<int>({difficulty[i], profit[i]}));
  }
  sort(begin(tasks), end(tasks));
  sort(begin(worker), end(worker));
  int max_profit = 0;
  int ttl_profit = 0;
  for (int i = 0, j = 0; i < worker.size(); ++i) {
    while (j < tasks.size() && worker[i] >= tasks[j][0]) {
      max_profit = max(max_profit, tasks[j][1]);
      ++j;
    }
    ttl_profit += max_profit;
  }
  return ttl_profit;
}

// subset的 找不重复的子集
// 78. Subsets
vector<vector<int>> sub_sets_;
void GetAllSubSets(const vector<int>& nums, const int idx, 
                   vector<int> cur_vec) {
  if (idx >= nums.size()) return;
  GetAllSubSets(nums, idx + 1, cur_vec);
  cur_vec.emplace_back(nums[idx]);
  sub_sets_.emplace_back(cur_vec);
  GetAllSubSets(nums, idx + 1, cur_vec);
}    
vector<vector<int>> subsets(vector<int>& nums) {
  GetAllSubSets(nums, 0, {});
  sub_sets_.push_back({});
  return sub_sets_;
}
// 90. Subsets II
vector<vector<int>> sub_sets_;
vector<int> cur_;  
void SubSets(const vector<int>& nums, const int idx) {
  if (idx >= nums.size()) {
    sub_sets_.push_back(cur_);
    return;
  }
  int i = idx + 1;
  while (i < nums.size() && nums[i] == nums[idx]) {
    ++i;
  }
  for (int j = idx; j <= i; ++j) {
    SubSets(nums, i);
    cur_.push_back(nums[idx]);
  }
  for (int j = idx; j <= i; ++j) {
    cur_.pop_back();
  }
}
vector<vector<int>> subsetsWithDup(vector<int>& nums) {
  sort(begin(nums), end(nums));
  SubSets(nums, 0);
  return sub_sets_;
}

// 矩阵迷宫start走到end，中间有路障，和金币，要求吃到所有金币，有多少种走法？一开始用iteration DFS，后来发现需要用backtrack
// https://blog.csdn.net/ProLayman/article/details/96325387

// Geometry
```

ML/DL preparation. http://usa.baidu.com/careers/?gh_jid=3048306 



-----------------------------------------------------------



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
bool searchMatrix(vector<vector<int>>& matrix, int target) {
  if (target < matrix[0][0] || target > matrix.back().back())
    return false;
  vector<int> column;
  for (int i = 0; i < matrix.size(); ++i) {
    column.emplace_back(matrix[i][0]);
  }
  auto it = lower_bound(begin(column), end(column), target);
  if (it == end(column) || (*it > target && it != begin(column)))
    --it;
  const int idx = it - begin(column);
  it = lower_bound(begin(matrix[idx]), end(matrix[idx]), target);
  return it != end(matrix[idx]) &&  *it == target;
}
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
// 224. Basic Calculator
pair<string, int> Parse(const string& s) {
  int new_idx = 0;
  stack<int> stk;
  int val = 0;
  int inc_size = 0;
  string res;
  string tmp = s;
  
  while (new_idx < tmp.size()) {
    if (tmp[new_idx] == '(')
      stk.push(new_idx);
    else if (tmp[new_idx] == ')') {
      const int left = stk.top();
      stk.pop();
      val = calculate(tmp.substr(left+1, new_idx - left - 1));
      const auto new_val = to_string(val);
      tmp = tmp.substr(0, left) + new_val + tmp.substr(new_idx + 1);
      const int tmp_inc_size = new_idx - left + 1 - new_val.size();
      new_idx -= tmp_inc_size;
      inc_size += tmp_inc_size;  
      res += new_val;
    } else {
      res += tmp[new_idx];
    }
    if (stk.empty())
      break;
    ++new_idx;
  }
  
  return { tmp.substr(0, new_idx+1), new_idx + inc_size};
}  
  
int calculate(string s) {
  int res = 0;
  char op = '+';
  int tmp = 0;
  bool has_num = false;
  
  for (int i = 0; i < s.size(); ++i) {
    if (s[i] == '(') {
      const auto [str, index] = Parse(s.substr(i));
      tmp += calculate(str);
      i += index;
      has_num = true;
    } else if (s[i] >= '0' && s[i] <= '9') {
      tmp = tmp*10 - '0' + s[i];
      has_num = true;
    } else if (s[i] == ' '){
      continue;
    } else {
      const bool is_con = !has_num?true:false;
      res += op == '-'? -1 * tmp : tmp;
      tmp = 0;
      op = is_con? (s[i] == op?'+':'-') : s[i] == '-'?'-':'+';
      has_num = false;
    }
  }
  res += op == '-'? -1 * tmp : tmp;
  
  return res;
}

// 227. Basic Calculator II
int calculate(string s) {
  int s_len = s.size();      
  int cur_res = 0;
  int fin_res = 0;
  char ope = '+';
  int cur_num = 0;  
  for (int idx = 0; idx < s_len; ++idx) {
     if (isdigit(s[idx])) {
       cur_num = cur_num*10 + (s[idx] - '0');
     }
     if (s[idx] == '*' || s[idx] == '/' || s[idx] == '+' 
          || s[idx] == '-' || idx == s_len - 1) {
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
       cur_num = 0;
       ope = s[idx];
     }
  }
  return fin_res;
}

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



```c++
// 51. N-Queens
static constexpr int kNum = 10;
char kQueen[kNum][kNum];
bool row[kNum] = {false};
bool col[kNum] = {false};  
bool dg[2*kNum] = {false};
bool udg[2*kNum] = {false};

void StoreResult(const int N, vector<vector<string>>& ans) {
  vector<string> sol;
  for (int i = 0; i < N; ++i) {
    string tmp;
    for (int j = 0; j < N; ++j) {
      tmp += kQueen[i][j];
    }
    sol.push_back(tmp);
  }
  ans.push_back(sol);
}  
  
void DFS(int x, int y, 
         const int n, const int N,
        vector<vector<string>>& ans) {
  if (n > N) return;
  if (y == N) {
    y = 0;
    ++x;
  }
  if (x == N) {
    if (n == N) {
      StoreResult(N, ans);
    }
    return;
  }
  kQueen[x][y] = '.';
  DFS(x, y+1, n, N, ans);
  if (!row[y] && !col[x] && !dg[x+y] && !udg[-y+x+N]) {
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = true;
    kQueen[x][y] = 'Q';
    DFS(x, y + 1, n+1, N, ans);
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = false;
    kQueen[x][y] = '.';
  }
}  
vector<vector<string>> solveNQueens(int n) {
  vector<vector<string>> ans;
  DFS(0, 0, 0, n, ans);
  return ans;
}

// Geometry part
```

