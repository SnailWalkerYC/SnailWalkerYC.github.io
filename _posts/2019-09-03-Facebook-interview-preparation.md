---
layout: post
title: Facebook interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



```c++
// Onsite preparation
// 26
// 297
// 695
// 958
// 20
// 34
// 31
// left most one
// verify alien ditionary
// Trapping rain water
// 42
// permutation
// next permutation
// find common elements in two array, 
// convert the tree to a flat list
// 162
// 567
// 给定一个list of commits, 找到最初的bug的那个commit
// LC1464的变种题目，找到maximum product in an array，可以是有negative integer
// Find value from bitonic array
4. System Design
Design a system that returns nearby places (similar to Yelp Design)

5. ML System Design
Design a ML system for delecting / hiding violent posts

6. System Design (Shadow Interview)
Design a system that crawls websites
  
// 246 不过输入是整数 给出数字 判断旋转180度后是不是还一样。 follow up 是给出一个长度，输出所有这么长的 旋转180读不变的数字
// 347, 37 二叉树iterator
// 297
// minimum meeting rooms
// 270, 76
// string to digits;   163
  
// 1. 给一个包含自然数的数组，要求找出数组内所有的连续数字序列，加起来等于给定的一个目标数字。
2. 给一个包含字符串的数组，然后要求找到这个数组里面所有符合某个pattern的字符串， pattern是"A.B.C.", 这里面每个“.”对应任意一个字符，字符串“ADBECF"符合，但是“ABECF”就不符合。
  
// 138 copy list with random ptr,
// LC 734 string similarity  LC 839, similar String Groups
// 设计一个Auto search suggestion +  top K 常见题目 用Trie + hashMap 存prefix 解决
// 1. coding: Minimum local, return index, best solution, binary search to see which side is guaranteed with an answer [hide==100]
2. Design simplified tweeter that supports post and search with exact match. for search, we can utilize trie structure for space-efficient search
3. Binary tree to doubly linked list
4. behavior, 外加一个简单的coding： add two numbers in string format
[/hide]
5. Find famous Facebook user in a group that 1. Does not follow anyone 2. Every one else is ollowing him/her 3. May be 0 or 1 famous person 这个应该是拓扑排序  
```







------------------



```c++
// 97. Interleaving String

// 25. Reverse Nodes in k-Group

// 30. Substring with Concatenation of All Words

// 87. Scramble String

// 93. Restore IP Addresses

// 115. Distinct Subsequences

// 126. Word Ladder II

// 158. Read N Characters Given Read4 II - Call multiple times

// 159. Longest Substring with At Most Two Distinct Characters

// 164. Maximum Gap

// 174. Dungeon Game
```



```c++
// Top list https://leetcode.com/problemset/all/?listId=7p59281

// 124. Binary Tree Maximum Path Sum
int MaxPathSum(TreeNode* root, int& max_sum) {
  if (root == nullptr) return 0;
  int left_sum = max(MaxPathSum(root->left, max_sum), 0);
  int right_sum = max(MaxPathSum(root->right, max_sum), 0);
  int root_val = root->val;
  max_sum = max(root_val + left_sum + right_sum, max_sum);
  return max(root_val + left_sum, root_val + right_sum);
}    
int maxPathSum(TreeNode* root) {
  int max_sum = INT_MIN;
  if (root != nullptr) 
    MaxPathSum(root, max_sum);
  return max_sum;
}

// 953. Verifying an Alien Dictionary
bool isAlienSorted(vector<string>& words, string order) {
  unordered_map<char, int> order_map;
  for (int i = 0; i < order.size(); ++i)
    order_map[order[i]] = i;
  for (int i = 1; i < words.size(); ++i) {
    if (Bigger(words[i-1], words[i], order_map))
      return false;
  }
  return true;
}
bool Bigger(const string& s1, const string& s2, 
            unordered_map<char, int>& order_map) {
  for (int i = 0; i < s1.size(); ++i) {
    if (s2.size() <= i) return true;
    if (order_map[s1[i]] > order_map[s2[i]])
      return true;
    else if (order_map[s1[i]] < order_map[s2[i]])
      return false;
  }
  return false;
}  

// 146. LRU Cache
// LRU cache
class LRUCache {
 public:
  unordered_map<int, pair<int, list<int>::iterator>> record_;
  list<int> key_;
  int capacity_ = 0;
  LRUCache(int capacity) {
    capacity_ = capacity;
  }
  int get(int key) {
    auto it = record_.find(key);
    if (it == record_.end()) return -1;
    const auto value = it->second;
    record_.erase(it);
    key_.erase(value.second);
    key_.push_front(key);
    record_[key] = make_pair(value.first, key_.begin());
    return value.first;
  } 
  void put(int key, int value) {
    auto it = record_.find(key);
    if (it != record_.end()) {
      const auto [_, it_list] = it->second;
      record_.erase(it);
      key_.erase(it_list);
    }
    if (key_.size() >= capacity_) {
      auto back_it = prev(key_.end());
      record_.erase(*back_it);
      key_.erase(back_it);
    }
    key_.push_front(key);
    record_[key] = std::make_pair(value, key_.begin());
  }
};

// 824. Goat Latin
bool isVowel(char c) {
  char c_ = tolower(c);
  return c_=='a' || c_=='e' || c_=='i' || c_=='o' || c_=='u'; 
}        
void procesString(string& str_cur, string& padding, string& ans) {
  if(!isVowel(str_cur[0]))
    str_cur = str_cur.substr(1) + str_cur[0];
    str_cur += "ma";        
    ans += " " + str_cur + padding;
    str_cur = "";
    padding += 'a';
}        
string toGoatLatin(string S) {
  string ans = "";
  string str_cur = "";
  string padding = "a";
  for(auto c:S) {
    if(isalpha(c)) {
      str_cur +=c;
    }    
    else if(str_cur.size()){
      procesString(str_cur, padding, ans);        
    }    
  }    
  if(str_cur.size()) {
    procesString(str_cur, padding, ans); 
  }              
  return ans.substr(1);
}

// Binary tree -> inorder, preorder, postorder.
// http://yuchenspace.info/tree-2/

// 24. Swap Nodes in Pairs
ListNode* swapPairs(ListNode* head) {
  ListNode* dummy_node = new ListNode(0);
  ListNode* cur_node = dummy_node;    
  while (head != nullptr && head->next != nullptr) {
    ListNode* next = head->next;
    ListNode* next_next= next->next;
    cur_node->next = next;
    next->next = head;
    head->next = next_next;
    cur_node = head;
    head = head->next;
  }    
  cur_node->next = head;    
  return dummy_node->next;
}

// 278. First Bad Version
int firstBadVersion(int n) {
  int stt_idx = 1;
  int fin_idx = n;
  while (stt_idx + 1 < fin_idx) {
    int mid_idx = stt_idx + (fin_idx - stt_idx) / 2;
    if (isBadVersion(mid_idx)) fin_idx = mid_idx;
    else stt_idx = mid_idx;
  }
  if (isBadVersion(stt_idx)) return stt_idx;
  if (isBadVersion(fin_idx)) return fin_idx;
  return -1;
}

// Merge intervals.
void Merge(std::vector<std::pair<int, int>>& segs) {
  std::vector<std::pair<int, int>> res;
  std::sort(begin(segs), end(segs)); 
  int st = -2e9;
  int ed = -2e9;
  for (const auto& seg : segs) {
    if (ed < seg.first) {
      if (st != -2e9) res.push_back({st, ed});
      st = seg.first;
      ed = seg.second;
    }
    else {
      ed = std::max(seg.second, ed);
    }  
  }
  if (st != -2e9) {
    res.push_back({st, ed});
  }
  res.swap(segs);
}

// 20. Valid Parentheses
bool isValid(string s) {
  stack<char> record;
  unordered_map<char, char> pattern;
  pattern[')'] = '('; 
  pattern[']'] = '[';
  pattern['}'] = '{';      
  for (auto c:s) {
    if (c == '(' || c == '[' || c == '{') {
      record.push(c);
    }
    else {
      if (record.empty() || record.top() != pattern[c]) {
        return false;
      }
      record.pop();
    }
  }
  return record.empty();
}

// 1120. Maximum Average Subtree
double max_ave_ = 0.0;
pair<int, double> CalculateMaxAverageSubtree(TreeNode* root) {
  if (!root) {
    return {0, 0};
  }
  const auto& [num_left, left] = CalculateMaxAverageSubtree(root->left);
  const auto& [num_right, right] = CalculateMaxAverageSubtree(root->right);
  if (num_left) {
    max_ave_ = max(max_ave_, left);
  }
  if (num_right) {
    max_ave_ = max(max_ave_, right);
  }
  const int ttl = num_left + num_right + 1;
  const double s = num_left * left + num_right * right + root->val;
  return {ttl, s/ttl};
}  
double maximumAverageSubtree(TreeNode* root) {
  const auto& [c, s] = CalculateMaxAverageSubtree(root);
  return max(max_ave_, c?s:0.0);
}

// 295. Find Median from Data Stream
class MedianFinder {
 public:
  priority_queue<int> low_part;
  priority_queue<int, vector<int>, greater<int>> high_part;  
  /** initialize your data structure here. */
  MedianFinder() {}
  void addNum(int num) {
    low_part.push(num);
    while (low_part.size() - high_part.size() > 1) {
      high_part.push(low_part.top());
      low_part.pop();
    }
  }  
  double findMedian() {
    if (low_part.size() > high_part.size()) {
      return low_part.top();
    }
    else {
      return low_part.top()*0.5 + high_part.top() * 0.5;
    }
  }
};

// 680. Valid Palindrome II
bool validPalindrome(string s) {
  int low=0;
  int high=s.size()-1;
  bool skip=false;
  while(low<high) {
    if(s[low]!=s[high]) {
      if(skip) {
        return false;
      }    
      if(s[low+1]==s[high] && s[low+2]==s[high-1]) {
        low+=2;
        high--;
        skip=true;
      }    
      else if(s[low]==s[high-1] && s[low+1]==s[high-2]) {
        high-=2;
        low++;
        skip=true;
      }    
      else {
        if(high-low==1 && !skip)
          return true;
        return false;
      }    
    }
    else {
      low++;
      high--;
    }    
  }    
  return true;
}

// 236. Lowest Common Ancestor of a Binary Tree
TreeNode* lowestCommonAncestor(TreeNode* root, 
                                   TreeNode* p, TreeNode* q) {
  if (!root || p == root || q == root) return root;
  const auto left = lowestCommonAncestor(root->left, p, q);
  const auto right = lowestCommonAncestor(root->right, p, q);
  return left && right? root:(left?left:right);
}

// 3. Longest Substring Without Repeating Characters
int record[256] = {0};  
int lengthOfLongestSubstring(string s) {
  int max_len = 0;
  memset(record, 0, sizeof record);
  for (int i = 0, j = 0; i < s.size(); ++i) {
    ++record[s[i]];
    while (record[s[i]] > 1) {
      --record[s[j]];
      ++j;
    } 
    max_len = max(max_len, i - j + 1);
  }
  return max_len;
}

// 415. Add Strings
string addStrings(string num1, string num2) {
  int cf = 0;
  string ans;
  int idx1 = num1.size() - 1;
  int idx2 = num2.size() - 1;
  while (cf || idx1 >= 0 || idx2 >= 0) {
    int cur = 0;
    cur += idx1 >= 0? num1[idx1] - '0':0;
    cur += idx2 >= 0? num2[idx2] - '0':0;
    cur += cf;
    --idx1;
    --idx2;
    ans = to_string(cur%10) + ans; 
    cf = cur/10;
  }
  return ans;
}

// 50. Pow(x, n)
double myPow(double x, int n) {
  if (!n) return 1;
  if (n == 1) return x;    
  if (n < 0) return 1.0 / (myPow(x, 1) * myPow(x, -1-n));    
  const auto half = myPow(x, n/2);
  return half * half * myPow(x, n & 1);
}
// https://blog.csdn.net/aaasssdddd96/article/details/111070875
// https://www.zhihu.com/question/51026869

// 173. Binary Search Tree Iterator
class BSTIterator {
 public:
  stack<TreeNode*> record;
  BSTIterator(TreeNode* root) {
    while (root != nullptr)  {
      record.push(root);
      root = root->left;
    }      
  }  
  /** @return the next smallest number */
  int next() {
    const auto tpr = record.top();
    const int tpr_val = tpr->val;
    record.pop();
    auto tpr_right = tpr->right;
    while (tpr_right != nullptr) {
      record.push(tpr_right);
      tpr_right = tpr_right->left;
    }
    return tpr_val;
  }
  /** @return whether we have a next smallest number */
  bool hasNext() {
    return record.size() > 0;    
  }
};

// 67. Add Binary
string addBinary(string a, string b) {
  string s = "";      
  int c = 0, i = a.size() - 1, j = b.size() - 1;
  while(i >= 0 || j >= 0 || c == 1) {
    c += i >= 0 ? a[i --] - '0' : 0;
    c += j >= 0 ? b[j --] - '0' : 0;
    s = char((c & 1) + '0') + s;
    c = c >> 1;
  }       
  return s;
}

// 210. Course Schedule II
vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
  unordered_map<int, vector<int>> graph;
  vector<int> indegrees(numCourses + 1, 0);
  for (const auto& pre : prerequisites) {
    graph[pre[1]].emplace_back(pre[0]);
    ++indegrees[pre[0]];
  }
  vector<int> ans;
  queue<int> que;
  for (int i = 0; i < numCourses; ++i) {
    if (!indegrees[i]) {
      ans.push_back(i);
      que.push(i);
    }
  }
  while (!que.empty()) {
    const int tp = que.front();
    que.pop();
    for (const auto& node : graph[tp]) {
      --indegrees[node];
      if (!indegrees[node]) {
        ans.push_back(node);
        que.push(node);
      }
    }
  }
  return ans.size() == numCourses? ans : vector<int>();
}

// 766. Toeplitz Matrix
bool isToeplitzMatrix(vector<vector<int>>& matrix) {
  for (int i = 1; i < matrix.size(); ++i) {
    for (int j = 1; j < matrix[0].size(); ++j) {
      if (matrix[i][j] != matrix[i-1][j-1])
        return false;
    }
  }        
  return true;
}

// 34. Find First and Last Position of Element in Sorted Array
int BinarySearch(vector<int>& nums, int target, bool status) {
  if (!nums.size()) return -1;
  int stt_idx = 0;
  int fin_idx = nums.size() - 1;
  int mid = -1;
  while (stt_idx + 1 < fin_idx) {
    mid = stt_idx + (fin_idx - stt_idx) / 2;
    if (nums[mid] > target) fin_idx = mid;
    else if (nums[mid] < target) stt_idx = mid;
    else if (!status) fin_idx = mid;
    else stt_idx = mid;
  }
  bool stt_status = nums[stt_idx] == target;
  bool fin_status = nums[fin_idx] == target;
  if (!status) {
    if (stt_status) return stt_idx;
    if (fin_status) return fin_idx;  
  } else {
    if (fin_status) return fin_idx;
    if (stt_status) return stt_idx;
  }
  return -1;
}
vector<int> searchRange(vector<int>& nums, int target) {
  int left_idx = BinarySearch(nums, target, 0);
  int right_idx = BinarySearch(nums, target, 1);
  return {left_idx, right_idx};
}

// 340. Longest Substring with At Most K Distinct Characters
int lengthOfLongestSubstringKDistinct(string s, int k) {
  int res = 0;
  unordered_map<int, int> record;
  for (int i = 0, j = 0; i < s.size(); ++i) {
    ++record[s[i]];
    while (record.size() > k) {
      --record[s[j]];
      if (!record[s[j]])
        record.erase(s[j]);
      ++j;
    }
    res = max(res, i - j + 1);
  }
  return res;
}

// 938. Range Sum of BST
int rangeSumBST(TreeNode* root, int L, int R) {
  if (root == nullptr) return 0;
  const auto cur_val = root->val;
  const bool in_range = cur_val >= L && cur_val <= R;
  return ((cur_val > R || in_range)? rangeSumBST(root->left, L, R) : 0) +
             ((cur_val < L || in_range)? rangeSumBST(root->right, L, R) : 0) +
             (in_range ? cur_val : 0);
}

// 311. Sparse Matrix Multiplication
vector<vector<int>> multiply(vector<vector<int>>& mat1, vector<vector<int>>& mat2) {
  unordered_map<int, unordered_set<int>> sparse_index1;
  unordered_map<int, unordered_set<int>> sparse_index2;
  const int k = mat1.size();
  const int n = mat2[0].size();
  for (int i = 0; i < mat1.size(); ++i) {
    for (int j = 0; j < mat1[0].size(); ++j) {
      if (mat1[i][j]) {
        sparse_index1[i].insert(j);
      }
    }
  } 
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < mat2.size(); ++j) {
      if (mat2[j][i]) {
        sparse_index2[i].insert(j);
      }
    }
  }
  vector<vector<int>> ans(k, vector<int>(n, 0));
  for (int i = 0; i < k; ++i) {
    for (int j = 0; j < n; ++j) {
      if (!sparse_index1[i].empty() && !sparse_index2[j].empty()) {
        const auto& j_col = sparse_index2[j];
        for (const auto& ele : sparse_index1[i]) {
          if (i == 1) cout << ele << " " << i << " " << j << endl;
          if (j_col.count(ele) > 0) {
            ans[i][j] += mat1[i][ele]*mat2[ele][j];
          }
        }
      }
    }
  }
  return ans;
}

// 339. Nested List Weight Sum
int depthSum2(vector<NestedInteger>& nestedList, int depth) {
  int cnt_sum = 0;    
  for (auto nest_list: nestedList) {
    if (nest_list.isInteger()) {
      cnt_sum += depth*nest_list.getInteger();
    }
    else {
      cnt_sum += depthSum2(nest_list.getList(), depth + 1);
    }
  }
  return cnt_sum;
}        
int depthSum(vector<NestedInteger>& nestedList) {
  int cnt_sum = depthSum2(nestedList, 1);     
  return cnt_sum;
}

// 285. Inorder Successor in BST
void inorderSuccessorNode(TreeNode* const root, TreeNode* const p, TreeNode* &ans) {
  if(!root) return;
  if (root->val > p->val) {
    if (ans == nullptr || root->val < ans->val) {
      ans = root;
    }
    inorderSuccessorNode(root->left, p, ans);  
  } else {
    inorderSuccessorNode(root->right, p, ans); 
  }
}
TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
  TreeNode* ans = nullptr;
  inorderSuccessorNode(root, p, ans);
  return ans;
}

// 268. Missing Number
int missingNumber(vector<int>& nums) {
  int num_len = nums.size();      
  nums.push_back(num_len);      
  if (!num_len) {
    return 0;
  }      
  for (int idx =0; idx < num_len; ++idx) {
    auto num = abs(nums[idx]);
    nums[num] = -abs(nums[num]);
  }      
  bool meet_zero = false;
  int idx_m = -1;
  for (int idx = 0; idx < nums.size(); ++idx) {
    if (nums[idx] > 0) {
      return idx;
     }
     else if(!nums[idx]) {
       meet_zero =true;
       idx_m = idx;
    }
  }  
  return meet_zero?idx_m:0;
}

// 270. Closest Binary Search Tree Value
void Search(TreeNode* root, const double target,
            double& cur_diff, int& cur_num) {
  if (!root) return;
  const auto diff = abs(root->val - target);
  if (diff < cur_diff) {
    cur_diff = diff;
    cur_num = root->val;
  }
  if (target < root->val) {
    Search(root->left, target,
            cur_diff, cur_num);
  } else {
    Search(root->right, target,
            cur_diff, cur_num);
  }
}  
int closestValue(TreeNode* root, double target) {
  double cur_diff = INT_MAX; 
  int cur_num = 0;
  Search(root, target, cur_diff, cur_num);
  return cur_num;
}

// 317. Shortest Distance from All Buildings
int shortestDistance(vector<vector<int>>& grid) {
  vector<vector<int>> grids(grid.size(), vector<int>(grid[0].size(), 0));
  vector<vector<int>> count(grid.size(), vector<int>(grid[0].size(), 0));
  const int dirs[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };
  int count_building = 0;
  int count_free = 0;
  for (int i = 0; i < grid.size(); ++i) {
    for (int j = 0; j < grid[0].size(); ++j) {
      if (!grid[i][j]) 
        ++count_free;
      if (grid[i][j] == 1) {
        ++count_building;
        auto tmp = grid;
        queue<vector<int>> cur_visiting;
        unordered_set<int> visited;
        visited.insert(i * grid[0].size() + j);
        cur_visiting.push({i, j, 0});
        while (!cur_visiting.empty()) {
          const auto tp = cur_visiting.front();
          cur_visiting.pop();
          ++count[tp[0]][tp[1]];
          grids[tp[0]][tp[1]] += tp[2];
          for (int k = 0; k < 4; ++k) {
            const int new_row = tp[0] + dirs[1][k];
            const int new_col = tp[1] + dirs[0][k];
            if (new_row < 0 || new_col < 0 || new_row >= grid.size() ||
                new_col >= grid[0].size() || grid[new_row][new_col] > 0)
              continue;
            if (visited.find(new_row * grid[0].size() + new_col) != visited.end())
              continue;
            cur_visiting.push({new_row, new_col, tp[2] + 1});
            visited.insert(new_row * grid[0].size() + new_col);
          }
        }
      }
    }
  }
  int min_dis = INT_MAX;
  for (int i = 0; i < grid.size(); ++i) {
    for (int j = 0; j < grid[0].size(); ++j) {
      if (!grid[i][j] && count[i][j] == count_building) {
        min_dis = min(min_dis, grids[i][j]);
      }
    }
  }
  return min_dis == INT_MAX || !count_free?-1:min_dis;
}

// 347. Top K Frequent Elements
// Priority-queue
vector<int> topKFrequent(vector<int>& nums, int k) {
  priority_queue<int, vector<int>, greater<int>> max_k;
  unordered_map<int,int> count;
  for(auto num:nums) 
    count[num]++;
  for(auto ele:count) {
    max_k.push(ele.second);
    if(max_k.size()>k)
      max_k.pop();
  }
  vector<int> res;
  int tp=max_k.top();
  for(auto ele:count) {
    if(ele.second>=tp)
      res.push_back(ele.first);
  }
  return res;
}
// Quick-select
void QuickSelect(vector<pair<int, int>>& freqs, const int k, const int l,
                 const int r) {
  if (l >= r) return;
  int i = l - 1;
  int j = r + 1;
  const int mid = freqs[(r - l)/2 + l].second;
  while (i < j) {
    do {
      ++i;
    } while (freqs[i].second > mid);
    do {
      --j;
    } while (freqs[j].second < mid);
  
    if (i < j) {
      swap(freqs[i], freqs[j]);
    }
  }
  return j+1 >= k? QuickSelect(freqs, k, l, j) : QuickSelect(freqs, k, j + 1, r);
}  
vector<int> topKFrequent(vector<int>& nums, int k) {
  unordered_map<int, int> cnt;
  for (const auto& num : nums) {
    ++cnt[num];
  }
  vector<pair<int, int>> freqs;
  for (const auto& val : cnt) {
    freqs.emplace_back(val);
  }
  QuickSelect(freqs, k, 0, freqs.size() - 1);

  if (!freqs.empty()) {
    freqs.erase(freqs.begin() + k, freqs.end());
  }
  vector<int> ans;
  for (const auto& f : freqs) {
    ans.emplace_back(f.first);
  }
  return ans;
}

// 109. Convert Sorted List to Binary Search Tree
ListNode* FindMiddlePrev(ListNode* head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    if (fast_node == nullptr || fast_node->next == nullptr) break;
      slow_node = slow_node->next;
  }
  return slow_node;
}
TreeNode* sortedListToBST(ListNode* head) {
  if (head == nullptr) return nullptr;
  if (head->next == nullptr) return new TreeNode(head->val);
    ListNode* middle_prev = FindMiddlePrev(head);
  ListNode* middle = middle_prev->next;
  middle_prev->next = nullptr;
  TreeNode* left = sortedListToBST(head);
  TreeNode* right = sortedListToBST(middle->next);
  TreeNode* root = new TreeNode(middle->val, left, right);
  return root;
}

// 238. Product of Array Except Self
vector<int> productExceptSelf(vector<int>& nums) {
  int nums_len = nums.size();
  if(!nums_len)
    return {};
  vector<int> ans(nums_len, 0);
  int cnt_zero = 0;
  int accum = 1;
  int idx = -1;
  for(int ind=0; ind<nums_len; ind++) {
    if(!nums[ind]) {
      idx = ind;
      cnt_zero++;
      if(cnt_zero>=2)
        return ans;
    }
    else 
      accum *= nums[ind];
  }
  if(cnt_zero==1) {
    ans[idx] = accum;
    return ans;
  }
  for(int ind=0; ind<nums_len; ind++)
    ans[ind] = accum/nums[ind];
  return ans;
}

// 42. Trapping Rain Water
int trap(vector<int>& heights) {
  if (heights.empty()) return 0;
  stack<int> record;
  int count = 0;
  for (int i = 0; i < heights.size(); ++i) {
    while (!record.empty() && heights[i] >= heights[record.top()]) {
      const int mid = record.top();
      record.pop();
      if (record.empty()) break;
      const int left = record.top();
      count += (min(heights[left], heights[i]) - heights[mid])*
               (i - left - 1);
    }
    if(record.empty() || heights[record.top()] > heights[i]) {
      record.push(i);
    } 
  }
  return count;
}

// 528. Random Pick with Weight
vector<int> accumulate_sum_;
vector<int> record_;    
Solution(vector<int>& weights) {
  for (const auto w : weights) {
    accumulate_sum_.push_back(accumulate_sum_.empty()?w : accumulate_sum_.back()+w);
  }
  record_ = vector<int>(weights.size(), 0);  
}    
int pickIndex() {
  const int random_weight = accumulate_sum_.back() * (static_cast<double>(rand()) / RAND_MAX);
  const int index = upper_bound(accumulate_sum_.begin(), accumulate_sum_.end(), random_weight) - begin(accumulate_sum_);      
  return min(index, (int)accumulate_sum_.size());  
}

// 249. Group Shifted Strings
class Solution {
 private:
  bool haveTheSameDistance(string str1, string str2) {
    if(str1.size()!=str2.size())
      return false;
    if(str1==str2)
      return true;
    if(str1=="" || str2=="")
      return false;
    int dis=(str2[0]-str1[0]+26)%26;
    for(int i=1; i<str1.size();i++) {
      int cur_dis=(str2[i]-str1[i]+26)%26;
      if(cur_dis!=dis)
        return false;
    }
    return true;    
  } 
 public:
  vector<vector<string>> groupStrings(vector<string>& strings) {      
    map<string,vector<string>> rec;
    for(auto str:strings) {
      bool isFind = false;
      for(auto ele:rec) {
        if(haveTheSameDistance(ele.first, str)) {
          rec[ele.first].push_back(str);
          isFind = true;
          break;
        }
      }
      if(!isFind)
        rec[str].push_back(str);
    }
    vector<vector<string>> ans;
    for(auto ele:rec) 
      ans.push_back(ele.second);
    return ans;
  }
  
// 426. Convert Binary Search Tree to Sorted Doubly Linked List
pair<Node*, Node*> ConnectToDoubleList(Node* root, bool flag) {
  if (!root) return {nullptr, nullptr};
  auto left = ConnectToDoubleList(root->left, false);
  if (left.second) {
    root->left = left.second;
    left.second->right = root;
  }
  auto right = ConnectToDoubleList(root->right, true);
  if (right.first) {
    root->right = right.first;
    right.first->left = root;
  }
  return {left.first?left.first:root, right.second?right.second:root};
}  
Node* treeToDoublyList(Node* root) {
  if (!root) return nullptr;
  ConnectToDoubleList(root, false);   
  Node* stt = root;
  Node* fin = root;
  while (stt->left) {
    stt = stt->left;
  }
  while (fin->right) {
    fin = fin->right;
  }
  stt->left = fin;
  fin->right = stt;
  return stt;
}
  
// 139. Word Break
void GetInitialStatus(string s, vector<vector<bool>>& record, vector<string>& wordDict) {
  for (const auto word : wordDict) {
    int pos = 0;
    while ( (pos = s.find(word, pos)) != string::npos) {
      record[pos][pos + word.size() - 1] = true;
      ++pos;
    }
  }
}    
bool wordBreak(string s, vector<string>& wordDict) {
  vector<vector<bool>> record(s.size(), vector<bool>(s.size(), false));
  GetInitialStatus(s, record, wordDict);
  for (int len = 0; len < s.size(); ++len) {
    for (int stt = 0; stt < len; ++stt) {
      if (record[0][stt] && record[stt + 1][len]) record[0][len] = true;
     }
  }
  return record[0][s.size() - 1];
}

// 269. Alien Dictionary
bool SearchEdges(const vector<string>& words, const int index,
                 unordered_map<char, unordered_set<char>>& edges) {
  if (!words.size()) return true;  
  char last = words[0][index];
  vector<string> history;
  for (int i = 0; i < words.size(); ++i) {
    if (words[i][index] != last) {
      edges[last].insert(words[i][index]);
      last = words[i][index];
      bool status = SearchEdges(history, index + 1, edges);
      if (!status) return false;
      history.clear();
    } else {
      if (edges.find(words[i][index]) == edges.end())
        edges[words[i][index]] = {};
    }
    if (words[i].size() > index + 1) 
      history.push_back(words[i]);
    else {
      if (history.size() > 0) return false;
    }
  }
  bool status = true;
  if (history.size() > 0) {
    status = SearchEdges(history, index + 1, edges);
  }
  return status;
}
string alienOrder(vector<string>& words) {
  unordered_map<char, unordered_set<char>> edges;
  bool status = SearchEdges(words, 0, edges);
  if (!status) return "";
  vector<int> indegrees(26, 0);
  vector<bool> ingraph(26, 0);
  for (const auto& [node, edge] : edges) {
    ingraph[node-'a'] = true;
    for (const auto& v : edge) {
      ++indegrees[v-'a'];
      ingraph[v-'a'] = true;
    }
  }
  queue<int> cur;
  for (int i = 0; i < 26; ++i) {
    if (ingraph[i] && !indegrees[i]) {
      cur.push(i);
    }
  }
  string ans;
  while (!cur.empty()) {
    const auto tp = cur.front();
    ans += char(tp + 'a');
    cur.pop();
    for (const auto& edge : edges[tp+'a']) {
      --indegrees[edge-'a'];
      if (!indegrees[edge-'a']) {
        cur.push(edge-'a');
      }
    }
  }
  for (int i = 0; i < 26; ++i) {
    if (indegrees[i]) return "";
  }
  return ans;
}  

// 76. Minimum Window Substring
string minWindow(string s, string t) {
  unordered_map<char, int> cnt;
  unordered_set<char> ts;
  for (const auto& c:t) { 
    --cnt[c];
    ts.insert(c);
  }
  int diff = cnt.size();
  int res = INT_MAX;
  int stt_idx = 0;
  for (int i = 0, j = 0; i < s.size(); ++i) {
    ++cnt[s[i]];
    if (ts.count(s[i])) {
      if (!cnt[s[i]]) {
        --diff;
      } 
      while(!ts.count(s[j]) || 
            (ts.count(s[j]) && cnt[s[j]] > 0)) {
        --cnt[s[j]];
        ++j;
      }
    }
    if (!diff) {
      if (i - j + 1 < res) {
        res = i - j + 1;
        stt_idx = j;
      }
    }
  }
  return res == INT_MAX?"":s.substr(stt_idx, res);
}  

// 1047. Remove All Adjacent Duplicates In String
string removeDuplicates(string s) {
  string cur;
  for (const auto& c : s) {
    if (!cur.empty() && c == cur.back()) {
      cur.pop_back();
    } else {
      cur.push_back(c);
    }
  }
  return cur;
}

// 313. Super Ugly Number
int nthSuperUglyNumber(int n, vector<int>& primes) {
  priority_queue<long long, vector<long long>, greater<long long>> pq;
  pq.push(1);
  unordered_set<long long> his({1});
  while (--n) {
    const auto tp = pq.top();
    // cout << tp << endl;
    pq.pop();
    for (const auto& p : primes) {
      if (!his.count(static_cast<long long>(p)*tp)) {
        pq.push(p * tp);
        his.insert(p * tp);
      }
    }
  }
  return pq.top();
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
    
// 1428. Leftmost Column with at Least a One  
int leftMostColumnWithOne(BinaryMatrix &bm) {
  const auto& dims = bm.dimensions();     
  const int row = dims[0];
  const int col = dims[1];
  int l = 0;
  int r = col - 1;
  unordered_map<int, int> record;
  while (l + 1 < r) {
    const int mid = l + (r - l)/2;
    int cnt = 0;
    for (int i = 0; i < row; ++i) {
      if (bm.get(i, mid)) {
        ++cnt;
      }
    }
    record[mid] = cnt;
    if (cnt >= 1) {
      r = mid;
    } else {
      l = mid;
    }
  }
  if (record.find(l) == record.end()) {
    for (int i = 0; i < row; ++i) {
      if (bm.get(i, l)) {
        ++record[l];
      }
    }
  }
  if (record.find(r) == record.end()) {
    for (int i = 0; i < row; ++i) {
      if (bm.get(i, r)) {
        ++record[r];
      }
    }
  }
  if (record[l] >= 1) return l;
  if (record[r] >= 1) return r;
  return -1;
}

// 152. Maximum Product Subarray  
int maxProduct(vector<int>& nums) {
  long long max_cur = nums[0];
  long long min_cur = nums[0];
  long long res = nums[0];
  for (int i = 1; i < nums.size(); ++i) {
    if (!nums[i]) {
      max_cur = min_cur = nums[i];
    } else {
      const int num1 = max_cur*nums[i];
      const int num2 = min_cur*nums[i];
      max_cur = max(max(num1, num2), nums[i]); 
      min_cur = min(min(num1, num2), nums[i]); 
    }
    res = max(res, max_cur);
  } 
  return res;
}  

// 523. Continuous Subarray Sum
  
  
// 621. Task Scheduler


// 560. Subarray Sum Equals K
int subarraySum(vector<int>& nums, int k) {
  vector<int> record(nums.size() + 1, 0);
  for (int i = 1; i <= nums.size(); ++i) {
    record[i] += record[i-1] + nums[i-1];
  }
  unordered_map<int, int> his;
  his[0] = 1;
  int cnt = 0;
  for (int i = 1; i <= nums.size(); ++i) {
    cnt += his[record[i] - k];
    ++his[record[i]];
  }
  return cnt;
}  

// 973. K Closest Points to Origin
// Quick-select
// Priority-queue

// 301. Remove Invalid Parentheses

// 140. Word Break II

// 314. Binary Tree Vertical Order Traversal

// 282. Expression Add Operators

// 691. Stickers to Spell Word

// 1091. Shortest Path in Binary Matrix
// 158  
  
// 919

// 921

// 1541  
  
// 126  
  
// 286. Walls and Gates
```

