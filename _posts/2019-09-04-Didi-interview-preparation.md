---
layout: post
title: Didi interview preparation
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false
---



```c++
// Leetcode 394. Decode string
string Repeat(const string& str, const int num) {
  if (num == 1) return str;
  if (!num) return "";
  const auto str_half = Repeat(str, num/2);
  return str_half + str_half + (num&1?str:"");
} 
  
string decodeString(string s, const int stt = 0) {
  string ans;
  int num = 0;
  for (int i = stt; i < s.size(); ++i) {
    if (s[i] >= 'a' && s[i] <= 'z') {
      ans += s[i];
    } else if (s[i] >= '0' && s[i] <= '9') {
      num = num * 10 + s[i] - '0';
    } else if (s[i] == '[') {
      int cnt = 1;
      const int left = i;
      while (cnt) {
        ++i;
        if (s[i] == ']')
          --cnt;
        if (s[i] == '[')
          ++cnt;
      }
      const string new_str = decodeString(s.substr(left+1, i - left - 1));
      ans += Repeat(new_str, num);
      num = 0;
    }
  }
  return ans;
}

// Leetcode 366. Find Leaves of Binary Tree
int CollectLeaves(const TreeNode* root, vector<vector<int>>& ans) {
  if (!root) return -1;
  
  const int left = CollectLeaves(root->left, ans);
  const int right = CollectLeaves(root->right, ans);
  const int level = max(left, right) + 1;
  if (ans.size() <= level) 
    ans.push_back({});
  ans[level].push_back(root->val);
  return level;
}  

// Leetcode 243. shortest word  distance
int shortestDistance(vector<string>& words, string word1, string word2) {
  int idx1 = -1;
  int idx2 = -1;
  int fin_dis = INT_MAX;
  for (int idx = 0; idx < words.size(); ++idx) {
    if (words[idx] == word1) {
      idx1 = idx;
    }
    if (words[idx] == word2) {
      idx2 = idx;
    }
    if (idx1!=-1 && idx2!=-1) {
      fin_dis = min(fin_dis, abs(idx1-idx2));
    }
  }      
  return fin_dis;
}

// Leetcode 23 Merge k Sorted Lists
// with priority_queue/divide conquer
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

// Leetcode 470. Implement Rand10() using Rand7(). Random number generator from other random generator
int rand10() {
  int result = 40;
  while (true) {
    const int cur = (rand7() - 1) * 7 + (rand7() - 1);
    if (cur < result) {
      result = cur;
      break;
    }
  }
  return result % 10 + 1;
}
// Generate methods:
// Implement randM() using randN() when M > N:
// Step 1: Use randN() to generate randX(), where X >= M. In this problem, I use 7 * (rand7() - 1) + (rand7() - 1) to generate rand49() - 1.
// Step 2: Use randX() to generate randM(). In this problem, I use rand49() to generate rand40() then generate rand10.
// Note: N^b * (randN() - 1) + N^(b - 1) * (randN() - 1) + N^(b - 2) * (randN() - 1) + ... + N^0 * (randN() - 1) generates randX() - 1, where X = N^(b + 1).
// More Examples
// (1) Implement rand11() using rand3():
public int rand11() {
    int result = 22;
    while (result >= 22) {result = 3 * 3 * (rand3() - 1) + 3 * (rand3() - 1) + (rand3() - 1);}
    return result % 11 + 1;
}
// Idea: rand3() -> rand27() -> rand22 -> rand11
// Time Comlexity: O(27/22)

// (2) Implement rand9() using rand7():
public int rand9() {
    int result = 45;
    while (result >= 45) {result = 7 * (rand7() - 1) + (rand7() - 1);}
    return result % 9 + 1;
}
// Idea: rand7() -> rand49() -> rand45() -> rand9()
// Time Comlexity: O(49/45)

// (3) Implement rand13() using rand6():
public int rand13() {
    int result = 26;
    while (result >= 26) {result = 6 * (rand6() - 1) + (rand6() - 1);}
    return result % 13 + 1;
}
// Idea: rand6() -> rand36() -> rand26 -> rand13()
// Time Comlexity: O(36/26)
  
// Course schedule/how to decide only one schedule(if no cycle, only | E | = | V | - 1
// 207. Course Schedule
bool DFS(const int i, vector<int>& visited, 
         unordered_map<int, vector<int>>& graph) {
  if (visited[i] == 2) return true;
  visited[i] = 1;
  for (const auto& node : graph[i]) {
    if (visited[node] == 1)
      return false;
    const bool res = DFS(node, visited, graph);
    if (!res) return res;
  }
  visited[i] = 2;
  return true;
}  
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
  unordered_map<int, vector<int>> graph;
  vector<int> visited(numCourses + 1, 0);
  for (const auto& pre : prerequisites) {
    graph[pre[0]].emplace_back(pre[1]);
  }
  for (int i = 0; i < numCourses; ++i) {
    if (!visited[i]) {
      const bool res = DFS(i, visited, graph);
      if (!res)
        return res;
    } 
  }
  return true;
}
// 210. Course Schedule II
// Method 1: topological sort
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
// Method 2: DFS
bool DFS(unordered_map<int, vector<int>>& graph, const int i, stack<int>& record,
         vector<int>& visited) {
  if (visited[i] == 2) return true;
  visited[i] = 1;
  for (const auto& n : graph[i]) {
    if (visited[n] == 1) return false;
    if (!DFS(graph, n, record, visited))
      return false;
  }
  visited[i] = 2;
  record.push(i);
  return true;
}  
vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
  stack<int> record;
  unordered_map<int, vector<int>> graph;
  vector<int> visited(numCourses + 1, 0);
  for (const auto& pre : prerequisites) {
    graph[pre[0]].emplace_back(pre[1]);
  }
  for (int i = 0; i < numCourses; ++i) {
    if (!visited[i]) {
      if (!DFS(graph, i, record, visited)) {
        return {};
      }
    }
  }
  vector<int> ans;
  while (!record.empty()) {
    ans.emplace_back(record.top());
    record.pop();
  }
  reverse(begin(ans), end(ans));
  return ans;
}
// 630. Course Schedule III*
int scheduleCourse(vector<vector<int>>& courses) {
  sort(begin(courses), end(courses), [](const vector<int>& v1, 
                                        const vector<int>& v2) {
    return v1[1] < v2[1];
  });        
  int res = 0;
  priority_queue<int> heap;
  for (const auto& c : courses) {
    res += c[0];
    heap.push(c[0]);
    if (res > c[1]) {
      res -= heap.top();
      heap.pop();
    }
  }
  return heap.size();
}

// 1462. Course Schedule IV
vector<bool> checkIfPrerequisite(int numCourses, vector<vector<int>>& prerequisites, vector<vector<int>>& queries) {
  vector<vector<bool>> path(numCourses+1, vector<bool>(numCourses+1, false));
  for (const auto& p : prerequisites) {
    path[p[0]][p[1]] = true;
  }      
  for (int i = 0; i < numCourses; ++i) {
    path[i][i] = true;
  }
  for (int k = 0; k < numCourses; ++k) {
    for (int i = 0; i < numCourses; ++i) {
      for (int j = 0; j < numCourses; ++j) {
        path[i][j] = path[i][j]  || (path[i][k] && path[k][j]);
      }
    }
  }
  vector<bool> res;
  for (const auto& q : queries) {
    res.push_back(path[q[0]][q[1]]);
  }
  return res;
}

// 373(多路归并) 
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
  if (nums1.empty() || nums2.empty()) return {};
  priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> heap;
  for (int i = 0; i < nums2.size(); ++i) {
    heap.push({nums2[i] + nums1[0], i, 0});
  }
  vector<vector<int>> res;
  while (k-- && heap.size()) {
    const auto tp = heap.top();
    res.push_back({nums1[tp[2]], nums2[tp[1]]});
    heap.pop();
    if (tp[2] + 1< nums1.size()) {
      heap.push({nums2[tp[1]] + nums1[tp[2]+1], tp[1], tp[2]+1});
    }
  }
  return res;
}

// 1439. Find the Kth Smallest Sum of a Matrix With Sorted Rows
// https://leetcode-cn.com/problems/find-the-kth-smallest-sum-of-a-matrix-with-sorted-rows/solution/bao-li-jie-fa-zui-xiao-dui-by-coldme-2/

// Google onsite Compress 2D array
// https://leetcode.com/discuss/interview-question/326564/Google-or-Onsite-interview-or-Compress-2D-Array/302395
// 1. Reserve all the orders
// flatten the whole array matrix
// sort the array, assume we get arr[i], i=0,n-1
// build the reverse map of arr[i]->i+1
// traverse the matrix and replace the value using the reversed map
// *2. Leetcode 1632. Rank Transform of a matrix
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

// Merge binary search tree
// https://www.geeksforgeeks.org/merge-two-bsts-with-limited-extra-space/ 
/* A utility function to print Inoder traversal of a Binary Tree */
void inorder(node *root) {
  if (root != NULL) {
    inorder(root->left);
    cout<<root->data<<" ";
    inorder(root->right);
  }
}
// The function to print data of two BSTs in sorted order
void merge(node *root1, node *root2) {
  // s1 is stack to hold nodes of first BST
  snode *s1 = NULL;
  // Current node of first BST
  node *current1 = root1;
  // s2 is stack to hold nodes of second BST
  snode *s2 = NULL;
  // Current node of second BST
  node *current2 = root2;
  // If first BST is empty, then output is inorder
  // traversal of second BST
  if (root1 == NULL) {
    inorder(root2);
    return;
  }
  // If second BST is empty, then output is inorder
  // traversal of first BST
  if (root2 == NULL) {
    inorder(root1);
    return ;
  }
  // Run the loop while there are nodes not yet printed.
  // The nodes may be in stack(explored, but not printed)
  // or may be not yet explored
  while (current1 != NULL || !isEmpty(s1) ||
         current2 != NULL || !isEmpty(s2)) {
    // Following steps follow iterative Inorder Traversal
    if (current1 != NULL || current2 != NULL ) {
      // Reach the leftmost node of both BSTs and push ancestors of
      // leftmost nodes to stack s1 and s2 respectively
      if (current1 != NULL) {
        push(&s1, current1);
        current1 = current1->left;
      }
      if (current2 != NULL) {
        push(&s2, current2);
        current2 = current2->left;
      }
    }
    else {
      // If we reach a NULL node and either of the stacks is empty,
      // then one tree is exhausted, ptint the other tree
      if (isEmpty(s1)) {
        while (!isEmpty(s2)) {
          current2 = pop (&s2);
          current2->left = NULL;
          inorder(current2);
        }
        return ;
      }
      if (isEmpty(s2)) {
        while (!isEmpty(s1)) {
          current1 = pop (&s1);
          current1->left = NULL;
          inorder(current1);
        }
        return ;
      }
      // Pop an element from both stacks and compare the
      // popped elements
      current1 = pop(&s1);
      current2 = pop(&s2);
      // If element of first tree is smaller, then print it
      // and push the right subtree. If the element is larger,
      // then we push it back to the corresponding stack.
      if (current1->data < current2->data) {
        cout<<current1->data<<" ";
        current1 = current1->right;
        push(&s2, current2);
        current2 = NULL;
      }
      else {
        cout<<current2->data<<" ";
        current2 = current2->right;
        push(&s1, current1);
        current1 = NULL;
      }
    }
  }
}

// 617. Merge Two Binary Trees
TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
  if(!t1)
    return t2;
  if(!t2)
    return t1;
  int val1 = t1?t1->val:0;
  int val2 = t2?t2->val:0;
  TreeNode* root = t1?t1:t2;
  root->val = val1+val2;
  root->left = mergeTrees(t1?t1->left:NULL, t2?t2->left:NULL);
  root->right = mergeTrees(t1?t1->right:NULL, t2?t2->right:NULL);
  return root;
}

// Queue: 907. Sum of Subarray Minimums; 
// 416. separate one array into two, make the sum of difference the smallest; 
// 1239. Maximum Length of a Concatenated String with Unique Characters; 
// https://leetcode.com/problems/remove-k-digits/ 
// 1293. Shortest Path in a Grid with Obstacles Elimination

// 815. Bus Routes
int numBusesToDestination(vector<vector<int>>& routes, int S, int T) {
  assert(routes.size() && routes[0].size() );
  unordered_map<int, unordered_set<int>> rec;
  for(int s1 = 0; s1<routes.size(); s1++) {
    for(int s2 = 0; s2<routes[s1].size(); s2++) 
      rec[routes[s1][s2]].insert(s1);
  }    
  // stop->count
  queue<pair<int,int>> que;
  que.push(make_pair(S, 0));
  unordered_set<int> visited;
  while(!que.empty()) {
    int stop = que.front().first;
    int num = que.front().second;
    que.pop();
    if(T==stop)
      return num; 
     for(auto route:rec[stop]) {
       for(auto bus:routes[route]) {
         if(visited.find(bus)==visited.end()) {
           visited.insert(bus);
           que.push(make_pair(bus, num+1));
         }       
       }  
       routes[route].clear();
    }    
  }
  return -1;
}

// 863. All Nodes Distance K in Binary Tree
vector<int> answer;
vector<int> distanceK(TreeNode* root, TreeNode* target, int K) {
  answer.clear();
  getDistance(root, target, K);
  return answer;
}
int getDistance(TreeNode* root, TreeNode* target, int K) {
  if (root == nullptr) {
    return -1;
  }
  if (root == target) {
    BFSGetNode(root, K);
    return 0;
  }      
  int left_dis = getDistance(root->left, target, K);
  int right_dis = getDistance(root->right, target, K);      
  if (left_dis >= 0) {
    if (left_dis == K - 1) {
      answer.push_back(root->val);
    }        
    BFSGetNode(root->right, K - left_dis - 2);
    return left_dis + 1;
  }
  if (right_dis >= 0) {
    if (right_dis == K - 1) {
      answer.push_back(root->val);
    }        
    BFSGetNode(root->left, K - right_dis - 2);
    return right_dis + 1;
  }
  return -1;
}    
void BFSGetNode(TreeNode* cur_root, int depth) {
  if (!cur_root) {
    return;
  }      
  if (!depth) {
    answer.push_back(cur_root->val);
    return;
  }      
  BFSGetNode(cur_root->left, depth - 1);
  BFSGetNode(cur_root->right, depth - 1);
}

// 364. Nested List Weight Sum II
int depthSumInverse(vector<NestedInteger>& nestedList) {
  int cum_sum = 0;
  int cur_sum = 0;      
  queue<vector<NestedInteger>> que;
  que.push(nestedList);
  while (!que.empty()) {
    int len = que.size();
    cum_sum += cur_sum;        
    for (int idx = 0; idx < len; ++idx) {
      vector<NestedInteger> tp = que.front();
      que.pop();
      for (auto ele: tp) {
        if (ele.isInteger()) {
          cur_sum += ele.getInteger();
          cum_sum += ele.getInteger();
        }
        else {
          que.push(ele.getList());
        }
      }
    }
  }
  return cum_sum;
}

// 323. find total number of connected component in undirected graph
class UnionFind {
public:
  vector<int> rec;
  vector<int> num;
  int comp;
    
  UnionFind(int n) {
    rec = vector<int>(n, 0);
    num = vector<int>(n, 1);
    for (int idx = 0; idx < n; ++idx) {
      rec[idx] = idx;
    }
    comp = n;
  }
  int findParent(int node) {   
    if (node != rec[node]) {
      rec[node] = findParent(rec[node]);
    }    
    return rec[node];
  }
  void unionEdges(vector<int> edge) {
    int p_1 = findParent(edge[0]);
    int p_2 = findParent(edge[1]);
    if (p_1 != p_2) {
      if (num[p_1] > num[p_2]) {
        num[p_1] += num[p_2];
        rec[p_2] = p_1;
      }
      else {
        num[p_2] += num[p_1];
        rec[p_1] = p_2;
      }
      --comp;
    }
  }
};
class Solution {
public:
  int countComponents(int n, vector<vector<int>>& edges) {
    if (n <= 0) {
      return 0;
    }    
    UnionFind* uf = new UnionFind(n);    
    for (auto edge:edges) {
      uf->unionEdges(edge);
    }
    return uf->comp;
  }
};

// 311. OOD: implement a 2D matrix class that supports a matrix multiplication function.
template <classname T>
class A {
  public:
    void Do(const T& t) {}
}
template void A::Do<int>(const int&);
template void A::Do<std::string>(const std::string&);

class Box {
 public:
  double getVolume(void) {
    return length * breadth * height;
  }
  void setLength( double len ) {
    length = len;
  }
  void setBreadth( double bre ) {
    breadth = bre;
  }
  void setHeight( double hei ) {
    height = hei;
  }  
  // Overload + operator to add two Box objects.
  Box operator+(const Box& b) {
    Box box;
    box.length = this->length + b.length;
    box.breadth = this->breadth + b.breadth;
    box.height = this->height + b.height;
    return box;
  }    
 private:
  double length;      // Length of a box
  double breadth;     // Breadth of a box
  double height;      // Height of a box
};
// Operation
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

// Leetcode. 218 The Skyline Problem
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
    if (pt[1] < 0) m.insert(-pt[1]);   // 左端点，高度入堆
    else m.erase(m.find(pt[1]));       // 右端点，高度出堆  
    const auto cur_max = *crbegin(m);  // 当前最大高度高度
    if (cur_max != prev_max) {         // 当前最大高度不等于最大高度perv表示这是一个转折点
      ans.push_back({pt[0], cur_max}); // 添加坐标
      prev_max = cur_max;              // 更新最大高度
    }
  }
  return ans;
}

// 1374. Generate a String With Characters That Have Odd Counts
string GenerateTheString(char c, const int n) {
  if (n == 1) return string(1, c);
  if (!n) return "";
  const string str = GenerateTheString(c, n/2);
  return str + str + (n&1? string(1, c):"");
}  
string generateTheString(int n) {
  if (n == 1) return "a";
  return n&1? GenerateTheString('b', n / 2 + 1) + 
         GenerateTheString('b', n / 2) :  GenerateTheString('b', n - 1) + 
          "c"; 
}

// 79. Word Search
int dir[4][2] = { {0, -1}, {1, 0}, {0, 1}, {-1, 0 } };
bool findWord(vector<vector<char>>& board,
              string word,
              int cur_idx,
              vector<vector<bool>>& rec,
              int r_idx,
              int c_idx) {
  if (cur_idx + 1 == word.size()) {
    return true;
  }      
  for (int idx = 0; idx < 4; ++idx) {
     int cur_r = dir[idx][1] + r_idx;
     int cur_c = dir[idx][0] + c_idx;
     if (cur_r < 0 || cur_r >= board.size() || cur_c < 0 || cur_c >= board[0].size() || rec[cur_r][cur_c]) {
       continue;
     }
     if (board[cur_r][cur_c] == word[cur_idx+1]) {
       if (cur_idx+2 == word.size()) {
         return true;
       }
       rec[cur_r][cur_c] = true;
       bool cur_res = findWord(board, word, cur_idx+1, rec, cur_r, cur_c);
       if (cur_res) {
         return cur_res;
       }
       rec[cur_r][cur_c] = false;   
    }
  }        
  return false;
}
bool exist(vector<vector<char>>& board, string word) {
  if (!board.size() || !board[0].size()) {
    return false;
  }          
  int row = board.size();
  int col = board[0].size();      
  for (int r = 0; r < row; ++r) {
    for (int c = 0; c < col; ++c) {
      if (board[r][c] == word[0] ) {
        vector<vector<bool>> rec(row, vector<bool>(col, false));
        rec[r][c] = true;
        if (findWord(board, word, 0, rec, r, c)) {
          return true;
        }
      }
    }
  }      
  return false;
}
// 212. Word Search II
struct Node {
  bool is_word = false;
  Node* child[26] = {nullptr};
};  
unique_ptr<Node> parent_ = make_unique<Node>();  
unordered_set<string> result_set_;
int m_ = 0;
int n_ = 0;
void ConstructTrie(const vector<string>& words) {
  for (const auto word : words) {
    Node* cur_node = parent_.get();
    for (int i = 0; i < word.size(); ++i) {
      if (!cur_node->child[word[i]-'a']) {
        cur_node->child[word[i]-'a'] = new Node();
      }
      if (i == word.size() - 1) {
        cur_node->child[word[i]-'a']->is_word = true;
      } 
      cur_node = cur_node->child[word[i]-'a'];
    }
  }
}
bool IsEmpltyChild(const Node* head) {
  for (int i = 0; i < 26; ++i) {
    if (head->child[i]) return false;
  }
  return true;
}  
const int dirs[2][4] = { {-1, 0, 1, 0}, {0, 1, 0, -1 } };  
vector<vector<char>> board_;
void Search(const int row, const int col, Node* cur_node,
            string cur_str, vector<vector<char>>& visited) {
  const int cur_idx = visited[row][col] - 'a';
  Node* child = cur_node->child[cur_idx];
  if (!child) return;
  if (child->is_word) result_set_.insert(cur_str);
  const auto c = visited[row][col];
  visited[row][col] = '*';
  for (int i = 0; i < 4; ++i) {
    const int new_row = row + dirs[0][i];
    const int new_col = col + dirs[1][i];
    if (new_row < 0 || new_row >= m_ || new_col < 0 || new_col >= n_) continue;
    if (visited[new_row][new_col] == '*') continue;
    if (child->child[visited[new_row][new_col] - 'a']) {
      Node* node = child->child[visited[new_row][new_col] - 'a'];
      const string new_str = cur_str + visited[new_row][new_col];
      if (node != nullptr) {
        Search(new_row, new_col, child, new_str, visited);
      }
    }
  }  
  visited[row][col] = c;
  if (IsEmpltyChild(child)) {
    delete child;
    cur_node->child[cur_idx] = nullptr;
  }
}  
void SearchInTrie(const int row, const int col) {
  Node* cur_node = parent_.get();
  string cur_str(1, board_[row][col]);
  Search(row, col, cur_node, cur_str, board_);
}  
public:
vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
  ConstructTrie(words);
  m_ = board.size();
  n_ = board[0].size();
  board_ = board;
  
  for (int i = 0; i < board.size(); ++i) {
    for (int j = 0; j < board[0].size(); ++j) {
      SearchInTrie(i, j);
    }
  }
  return vector<string>(result_set_.begin(), result_set_.end());
}

// 1200. Minimum Absolute Difference
vector<vector<int>> minimumAbsDifference(vector<int>& arr) {
  if (arr.empty()) {
    return {};
  }
  std::sort(arr.begin(), arr.end());      
  vector<vector<int>> min_pair;
  int cur_diff = INT_MAX;      
  for (int ind = 1; ind < arr.size(); ++ind) {
    if (arr[ind] - arr[ind-1] == cur_diff) {
      min_pair.emplace_back(vector<int>({arr[ind-1], arr[ind]}));
    }    
    else if (arr[ind] - arr[ind-1] < cur_diff) {
      cur_diff = arr[ind] - arr[ind-1];
      min_pair.clear();
      min_pair.emplace_back(vector<int>({arr[ind-1], arr[ind]}));
    }
  }
  return min_pair;
}

// 340. Longest Substring with At Most K Distinct Characters if streaming.
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

// 85. maximum rectangle
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

// 116. Populating Next Right Pointers in Each Node
Node* connect(Node* root) {
  if (!root) return root;
  if (root->next) {
    if (root->right && root->next->left) {
      root->right->next = root->next->left;
    }
  }
  if (root->left && root->right) 
    root->left->next = root->right;
  connect(root->left);
  connect(root->right);
  return root;
}

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

// 133. Clone Graph
unordered_map<Node*, Node*> record;
Node* cloneGraph(Node* node) {
  if (!node) return nullptr;
  if (record.count(node) > 0) 
    return record[node];
  vector<Node*> new_neighbors;
  record[node] = new Node(node->val);
  for (const auto& n : node->neighbors) {
    record[n] = cloneGraph(n);
    new_neighbors.push_back(record[n]);
  }
  record[node]->neighbors.swap(new_neighbors);
  return record[node];
}

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

// 402. Remove K Digits
string removeKdigits(string num, int k) {
  stack<char> stk;
  const int K = k;
  int index = 0;
  for (const auto& c : num) {
    while (!stk.empty() && stk.top() > c) {
      stk.pop();
      --k;
      if (!k)
        break;
    }
    if (!k) break;
    stk.push(c);
    ++index;
  }
  string ans;
  while (!stk.empty()) {
    ans = string(1, stk.top()) + ans;
    stk.pop();
  }
  ans += num.substr(index);
  ans = ans.substr(0, num.size() - K);
  string ans2;
  index = 0;
  while (index < ans.size()) {
    if (ans[index] != '0') {
      ans2 += ans.substr(index);
      break;
    }
    ++index;
  }
  return ans2.empty() ? "0" : ans2;
}
```

