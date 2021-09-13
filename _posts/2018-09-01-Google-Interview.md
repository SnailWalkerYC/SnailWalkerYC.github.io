---
layout: post
title: Google Interview
date: 2018-09-01
categories: [technology]
tags: [tech]
comments: false
---



```c++
// Series meeting room & house rober & series trees
// 总共有 N个会议室，问能否满足给定schedule的需求
// 252. Meeting Rooms
bool canAttendMeetings(vector<vector<int>>& intervals) {
  map<int, int> intervals_mp;
  for (const auto& interval : intervals) {
    ++intervals_mp[interval[0]];
    --intervals_mp[interval[1]];
  }
  int count = 0;
  for (const auto& interval : intervals_mp) {
    count += interval.second;
    if (count > 1) return false;
  }
  return true;
}

static bool compareAll(const vector<int>& v1, const vector<int>& v2) {
  return v1[0] < v2[0] || (v1[0]==v2[0] && v1[1] < v2[1]);
}    
bool canAttendMeetings(vector<vector<int>>& intervals) {
  if (!intervals.size()) {
    return true;
  }      
  sort(intervals.begin(), intervals.end(), compareAll);      
  int cur_max = INT_MIN;
  for (auto ele: intervals) {
    if (ele[0] < cur_max) {
      return false;
    }
    cur_max = ele[1];
  }
  return true;
}
// 253. Meeting Rooms II
int minMeetingRooms(vector<vector<int>>& intervals) {
  map<int, int> record;
  for (const auto& interval : intervals) {
    ++record[interval[0]];
    --record[interval[1]];
  }
  int count = 0;
  int max_count = 0;
  for (const auto& interval : record) {
    count += interval.second;
    max_count = max(max_count, count);
  }
  return max_count;
}
// Meeting room 3. Given a list of intervals calendar and a number of available conference rooms. For each query, return true if the meeting can be added to the calendar successfully without causing a conflict, otherwise false. A conference room can only hold one meeting at a time.
// Input: calendar = [[1, 2], [4, 5], [8, 10]], rooms = 1, queries = [[2, 3], [3, 4]]
// Output: [true, true]
// 732. My Calendar III
class MyCalendarThree {
 public:
  map<int, int> record_;
  
  MyCalendarThree() {}
    
  int book(int start, int end) {
    ++record_[start];
    --record_[end];
    int count = 0;
    int max_count = 0;
    for (auto it = record_.begin(); it != record_.end(); ++it) {
      count += it->second;
      max_count = max(count, max_count);
    }
    return max_count;
  }
};
// 729. My Calendar I
// 731. My Calendar II
class MyCalendarTwo {
 private:
  map<int, int> record_;
  int threshold_ = 2;
  
 public:
  MyCalendarTwo() {}
    
  bool book(int start, int end) {
    ++record_[start];    
    --record_[end];
    int count = 0;
    for (const auto& [ts, num] : record_) {
      count += num;
      if (count > threshold_) {
        --record_[start];
        ++record_[end];
        return false;
      }
    }
    return true;
  }
};
```



```c++
// 1254. Number of Closed Islands
让求最大的陆地面积，1表示陆地0表示水域。如果一圈陆地包围一片水域那么这块水域也算这个岛的面积。先给出了2pass做法。先遍历四条边界，以边界上的水域（0）开始dfs把与边界相连的水域标记为-1表示无效。再全盘遍历，以1开始dfs无论01向四面扩展计数找岛的面积，然后global max记录最大岛面积。说完小哥说能不能1pass解决。解法大概是全盘遍历，以1开始dfs无论01向四面扩展计数找岛的面积，如果触及边界上的水域就返回-1表示无效，然后把四面valid的面积累加。写了code，自己fix了一个bug。
  
int closedIsland(vector<vector<int>>& grid) {
  int ans = 0;
  for (int i = 0; i < grid.size(); ++i) {
    for (int j = 0; j < grid[0].size(); ++j) {
      if (grid[i][j] == 0) {
        ans += DFS(i, j, grid);
      }
    }
  }
  return ans;
}
bool DFS(const int row, const int col, vector<vector<int>>& grid) {
  if (row < 0 || col < 0 || 
      row >= grid.size() || col >= grid[0].size())
    return false;
  if (grid[row][col])
    return true;
  grid[row][col] = 1;
  const bool up_s = DFS(row - 1, col, grid);
  const bool down_s = DFS(row + 1, col, grid);
  const bool left_s = DFS(row, col - 1, grid);
  const bool right_s = DFS(row, col + 1, grid);
  return up_s && down_s && left_s && right_s; 
}  

// 就是给一个 n x m 2d array, 每个位置代表房子的金额 >= 0, 如果偷了一个房子, 则周围八个房子都不能偷, 问能偷到的最大金额是多少?
// 198. House Robber
int rob(vector<int>& nums) {
  int num1 = 0;
  int num2 = 0;
  for (const auto& c : nums) {
    int tmp = num2;
    num2 = max(num2, c + num1);
    num1 = tmp;
  }
  return max(num1, num2);
}
// 213. House Robber II
int rob(vector<int>& nums) {
  if(!nums.size())
    return 0;
  if(nums.size()==1)
    return nums[0];
  int accum_1 = 0;
  int accum_2 = 0;
  int ans[2] = {0};
  for(int ind=0; ind<nums.size()-1;ind++) {
    int tmp=accum_2;
    accum_2 = max(accum_2, accum_1+nums[ind]);
    accum_1 = tmp;
  }      
  ans[0] = accum_2;
  accum_1 = 0;
  accum_2 = 0;
  for(int ind=1; ind<nums.size();ind++) {
    int tmp=accum_2;
    accum_2 = max(accum_2, accum_1+nums[ind]);
    accum_1 = tmp;
  }
  ans[1] = accum_2;
  return *max_element(ans);
}
// 337. House Robber III
pair<int, int> Rob(const TreeNode* root) {
  if (!root)
    return {0, 0};
  const auto [l1, r1] = Rob(root->left);
  const auto [l2, r2] = Rob(root->right);
  return {r1 + r2, max(l1 + l2 + root->val, r1 + r2)};
}    
int rob(TreeNode* root) {
  const auto [num1, num2] = Rob(root);
  return max(num1, num2);
}

// 1349. Maximum Students Taking Exam
```



```c++
// given a list of circles, upper bound of a tunnel, 返回一个boolean问这些circles有没有block这个tunnel, circles有坐标(x,y)和半径radius。tunnel的lower bound是0, upper bound是Y
// Union find.  
```



```c++
// 1231. Divide Chocolate
// Similar problem: https://blog.csdn.net/qq_21201267/article/details/107534172
int maximizeSweetness(vector<int>& sweetness, int k) {
  const int s = accumulate(begin(sweetness), end(sweetness), 0);
  int left = 1;
  int right = s / (k + 1);
  while (left + 1 < right) {
    const int mid = left + (right - left) / 2;
    if (Check(mid, sweetness, k+1)) {
      left = mid;
    } else {
      right = mid;
    }
  }
  if (Check(right, sweetness, k+1)) return right;
  else return left;
} 
bool Check(const int target, vector<int>& sweetness, const int k) {
  int count = 0;
  int cur = 0;
  for (const auto& c : sweetness) {
    cur += c;
    if (cur >= target) {
      ++count;
      cur = 0;
      if (count >= k) return true;
    }
  }
  return false;
}  

// 410. Split Array Largest Sum
int splitArray(vector<int>& nums, int m) {
  const int s = accumulate(begin(nums), end(nums), 0);   
  int left = s/m;
  int right = s - m + 1;
  while (left + 1 < right) {
    const int mid = left + (right - left)/2;
    if (Check(mid, nums, m)) {
      right = mid;
    } else {
      left = mid;
    }
  }
  if (Check(left, nums, m)) return left;
  return right;
}
bool Check(const int target, const vector<int>& nums, const int m) {
  int cur = 0;
  int count = 0;
  for (const auto& c:nums) {
    if (c > target) return false;
    cur += c;
    if (cur > target) {
      ++count;
      cur = c;
    } else if (cur == target) {
      ++count;
      cur = 0;
    }
    if (count > m) return false;
  }
  if (cur > 0) ++count;
  return count <= m;
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
```



```c++
// Lintcode 
// coins in a line III
```



```c++
// 301 . Remove Invalid Parentheses
```



```c++
// 给定BST, 增加和删除一个结点
// 450. Delete Node in a BST
TreeNode* deleteNode(TreeNode* root, int key) {
  if (!root) return nullptr;
  
  TreeNode* parent = nullptr;
  TreeNode* cur = root;
  while (cur != nullptr) {
    if (cur->val > key) {
      parent = cur;
      cur = cur->left;
    } else if (cur->val < key) {
      parent = cur;
      cur = cur->right;
    } else {
      break;
    }
  }
  if (!cur) return root;
  TreeNode* mark = cur;
  if (cur->left) {
    parent = cur;
    cur = cur->left;
    while (cur->right) {
      parent = cur;
      cur = cur->right;
    }
    mark->val = cur->val;
    if (parent->left == cur)
      parent->left = cur->left;
    else parent->right = cur->left;
  } else if (cur->right) {
    parent = cur;
    cur = cur->right;
    while (cur->left) {
      parent = cur;
      cur = cur->left;
    }
    mark->val = cur->val;
    if (parent->right == cur)
      parent->right = cur->right;
    else
      parent->left = cur->right;
  } else {
    if (!parent) return nullptr;
    if (parent->left == cur) parent->left = nullptr;
    if (parent->right == cur) parent->right = nullptr;
  }
  return root;
}

// 701. Insert into a Binary Search Tree
TreeNode* insertIntoBST(TreeNode* root, int val) {
  if (!root) return new TreeNode(val);
  if (root->val >= val) {
    root->left = insertIntoBST(root->left, val);
  } else {
    root->right = insertIntoBST(root->right, val); 
  }
  return root;
}
```



```c++
// 68. Text Justification
string Generate(const int num) {
  if (!num) return "";
  else if (num == 1) return " ";
  const string& space_str = Generate(num/2);
  return space_str + space_str + (num&1?" ": "");
}  
  
vector<string> fullJustify(vector<string>& words, int max_width) {
  vector<string> ans;
  vector<string> cur;
  int cur_len = 0;
  for (const auto& word :words) {
    if (word.size() + cur_len + cur.size() <= max_width) {
      cur.emplace_back(word);
      cur_len += word.size();
    } else {
      string str;
      int rem = cur.size() == 1? 0 : (max_width - cur_len) % (cur.size() - 1);
      int space = cur.size() == 1? max_width - cur_len : (max_width - cur_len) / (cur.size() - 1);
      string space_str = Generate(space);
      string rem_str = Generate(1);
      for (int i = 0, j = 0; i < cur.size() - 1; ++i, ++j) {
        string cur_space = space_str;
        if (j < rem) {
          cur_space += rem_str;
        }
        str += cur[i] + cur_space;
      }
      str += cur.back();
      if (cur.size() == 1)
        str += space_str;
      ans.emplace_back(str);
      cur_len = word.size();
      cur.clear();
      cur.emplace_back(word);
    }
  }
  if (!cur.empty()) {
    string space_str = max_width >= cur_len + cur.size()?Generate(max_width - cur_len - cur.size()):"";
    string str;
    for (const auto& s : cur) {
      str += s + " ";
    }
    str = str.substr(0, max_width);
    str += space_str;
    ans.emplace_back(str);
  }
  return ans;
}
```



```c++
// 球赛，elimination tournament。给N个球队，和每两支球队1v1各自获胜的概率。求其中一支球队最后获得总冠军的概率。粗略解法是recursion
// Permutation & calculate the probability.
```



```c++
// 1275. Find Winner on a Tic Tac Toe Game
string tictactoe(vector<vector<int>>& moves) {
  vector<vector<int>> record(8, vector<int>(2, 0));
  int is_a = 0;
  for (const auto& m : moves) {
    ++record[m[0]][is_a];
    ++record[m[1]+3][is_a];
    if (m[0] == m[1])
      ++record[6][is_a];
    if (m[0] + m[1] == 2)
      ++record[7][is_a];
    is_a = 1 - is_a;
  }
  for (int i = 0; i < 8; ++i) {
    if (record[i][0] == 3)
      return "A";
    if (record[i][1] == 3)
      return "B";
  }
  return moves.size() == 9?"Draw":"Pending";
}

// 794. Valid Tic-Tac-Toe State
bool validTicTacToe(vector<string>& board) {
  int X_num = 0;
  int O_num = 0;
  int state[8][2] = {0};
  for (int ind1=0; ind1<3; ind1++) {
    for (int ind2=0; ind2<3; ind2++) {
      if (board[ind1][ind2]=='X') {
        state[ind1][0]++;
        state[ind2+3][0]++;
        if (ind1==ind2)
          state[6][0]++;
        if (ind1+ind2==2)
          state[7][0]++;
        X_num++;
      }
      else if(board[ind1][ind2]=='O'){
        state[ind1][1]++;
        state[ind2+3][1]++;
        if (ind1==ind2)
          state[6][1]++;
        if (ind1+ind2==2)
          state[7][1]++;
        O_num++;
      }
    }
  }
  if ((X_num != O_num && X_num != O_num+1))
      return false;
  int is_end_x = 0;
  int is_end_o = 0;
  for (int ind2=0; ind2<8; ind2++) {
    if (state[ind2][0]==3)
      is_end_x++;
    if (state[ind2][1]==3)
      is_end_o++;
  }
  if (is_end_x > 0 && is_end_o > 0)
    return false;
  if (!is_end_o && X_num-O_num==1)
    return true;
  if (!is_end_x && X_num==O_num)
    return true;
  return false; // is_end_x<=2 && is_end_o <=2;
}

// 997. Find the Town Judge
int findJudge(int N, vector<vector<int>>& trust) {
  std::vector<int> record_in(N+1, 0);
  std::vector<int> record_out(N+1, 0);      
  for (auto link:trust) {
    record_in[link[1]]++;
    record_out[link[0]]++;
  }      
  std::vector<int> candidate;
  for (int idx = 1; idx <= N; idx++) {
    if (record_out[idx]) {
      if (record_in[idx]==N-1) {
        return -1;
      }
    }
    else {
      if (record_in[idx] == N-1) {
        candidate.push_back(idx);
        if (candidate.size() > 1) {
          return -1;
        }
      }
    }
  }      
  return candidate.size()==1?candidate[0]:-1;
}
```



```c++
// 1102. Path With Maximum Minimum Value
class Solution { 
 public:
  class UnionFind {
   public: 
    UnionFind(const int n) {
      for (int i = 0; i < n; ++i) 
        record_.emplace_back(i);
      rank_ = vector<int>(n, 0);
    }
    
    int Find(const int num) {
      if (num != record_[num])
        record_[num] = Find(record_[num]);
      return record_[num];
    }
    
    void Union(const int a, const int b) {
      const int p_a = Find(a);
      const int p_b = Find(b);
      if (p_a == p_b) return;
      if (rank_[p_a] > rank_[p_b]) {
        record_[p_b] = p_a;
      } else {
        record_[p_a] = p_b;
        if (rank_[p_a] == rank_[p_b]) {
          ++rank_[p_b];
        }
      }
    }
    
    vector<int> record_;
    vector<int> rank_;
  };
  
  int maximumMinimumPath(vector<vector<int>>& A) {
    const int row = A.size();
    const int col = A[0].size();
    vector<vector<int>> edges;
    for (int i = 0; i < row; ++i) {
      for (int j = 0; j < col; ++j) {
        if (i > 0) {
          edges.push_back({(i-1)*col + j, i*col + j, min(A[i-1][j], A[i][j])});
        }
        if (j > 0) {
          edges.push_back({i*col+j-1, i*col+j, min(A[i][j-1], A[i][j])});
        }
      }
    }
    sort(begin(edges), end(edges), [](const vector<int>& a, const vector<int>& b) {
      return a[2] > b[2];
    });
    UnionFind uf(row * col);
    int max_val = 0;
    for (const auto& edge : edges) {
      if (uf.Find(0) == uf.Find(row*col-1)) {
        return max_val;
      }
      uf.Union(edge[0], edge[1]);
      max_val = edge[2];
    }
    return 0;
  }
};

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
```



```c++
// 781. Rabbits in Forest
int numRabbits(vector<int>& answers) {
  unordered_map<int, int> count;
  for (const auto& c : answers) {
    ++count[c];
  }
  int res = 0;
  for (const auto& [k, c] : count) {
    res += (c + k) / (k + 1) * (k + 1);
  }
  return res;
}

// 871. Minimum Number of Refueling Stops


// 57. Insert Interval
vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
  auto low_it = lower_bound(begin(intervals), end(intervals), newInterval, [](
       const vector<int>& v1, const vector<int>& v2) {
    return v1[1] < v2[0];
  });
  auto high_it = upper_bound(begin(intervals), end(intervals), newInterval, [](
      const vector<int>& v1, const vector<int>& v2) {
    return v1[1] < v2[0];
  });
  if (low_it == high_it) {
    intervals.insert(low_it, newInterval);
  } else {
    --high_it;
    (*high_it)[0] = min((*low_it)[0], newInterval[0]);
    (*high_it)[1] = max((*high_it)[1], newInterval[1]);
    intervals.erase(low_it, high_it);
  }
  return intervals;
}
// 1272. Remove Interval

// 56. Merge Intervals
vector<vector<int>> merge(vector<vector<int>>& intervals) {
  sort(begin(intervals), end(intervals), [](
       vector<int>& v1, vector<int>& v2) {
    return v1[0] < v2[0];
  });
  vector<vector<int>> ans;
  int stt = intervals[0][0];
  int ed = intervals[0][1];
  for (const auto& c : intervals) {
    if (c[0] <= ed) {
      ed = max(c[1], ed);
      stt = min(stt, c[0]);
    } else {
      ans.push_back(vector<int>({stt, ed}));
      ed = c[1];
      stt = c[0];
    }
  }
  ans.push_back({stt, ed});
  return ans;
}

// 436. Find Right Interval
vector<int> findRightInterval(vector<vector<int>>& intervals) {
  unordered_map<int, int> record;
  vector<int> keys;
  int cnt = 0;
  for (const auto& interval : intervals) {
    record[interval[0]] = cnt;
    keys.emplace_back(interval[1]);
    ++cnt;
  }
  sort(begin(intervals), end(intervals));
  vector<pair<int, int>> intervals2;
  for (const auto& interval:intervals) {
    intervals2.emplace_back(make_pair(interval[0], interval[1]));
  }
  vector<int> ans(intervals.size(), -1);
  for (int i = 0; i < intervals2.size(); ++i) {
    auto it = lower_bound(begin(intervals2), end(intervals2), 
                                make_pair(keys[i], keys[i]), 
                                [](const pair<int, int>& v1, 
                                   const pair<int, int>& v2) {
      return v1.first < v2.first;
    });
    if (it != end(intervals2)) {
      ans[i] = record[it->first];
    }
  }
  return ans;
}

// Like 317.
// a. given an array with 0/1, return the minimal distance between two '1';
// b. given an array with 0/1, return a new array, each field is the distance to closet '1';
// Input:   [1 0 1 0 0 0 1 1 0 1 0]
// output:[0 1 0 1 2 1 0 0 1 0 1]
// c. given a 2-D array with 0/1/2, return the minimal distance between '1' and '2'

第二轮code
a. Find the minimal distance from m to n
input: source/targte a, e
            2D array contains links between cities, { {a,b}, {b,c}, {c,d}, {d,e}, {b,d} }
Return: 3
b. the car may to refill now, with two more parameters, the size of tank(m), the list of cities with gas station;
return the minimal distance
用minHeap ＋ BFS。由于没有要求最少加油次数或是钱数。所以碰到加油站就该把油加满。
用个class保存<city, remainGas>,
由于是BFS + minHeap， minHeap已经优先处理了minDistance的情况， 所以BFS只需判断remainGas是否增加了。如果增加了，就重新加到队列里。newRemainGas--;
for (int city: neighbers) {
    if (!city.visited || newRemainGas > city.remainGas) {
        city.visited = true;
        city.remainGas = newRemainGas;
        minHeap.push(new int[]{city, distance + 1});
    }
}
```



```c++

题目：
Given a binary file which is 4GB, find out the shortest sequence which is not presented in the file.
        
Example1: A file has only 0x00
[0x00, 0x00, …, 0x00] --> [0x01]
        
Example2: A file has 0x00 to 0xFF (increasing from 0x00 to 0xFF, and then start from 0x00 again…)
[0x00, 0x01, 0x02, …, 0xFF, 0x00, 0x01, 0x02, …, 0xFF, …] --> [0x01, 0x00]
        
Note: The binary in the file can be any order.

我一开始是说，文件太大，所以我考虑generate sequence，然后检查有没有存在于这个file里。但问题还是文件太大，每次检查很麻烦。而且也要generate太多sequence了。
然后他给我第一个提示，最坏情况下，这个shortest sequence是多长？
我：整个文件那么长？
他：换个说法，如果是100byte的文件，有可能放满所有两个binary的组合吗？
        
2个binary，8*2bytes，2^16种可能，需要65536bytes，所以，100byte的文件，没法枚举完所有两个binary的组合。
同理，4GB的file，多少个binary的组合能让它没法枚举完。2^(x*8) > 4 * 10^9
他直接给我说答案是 x=4
// https://zhuanlan.zhihu.com/p/112306941   
distributed & map  
```



```c++
// buy stock
// See Pony ai summary.

// 951. Flip Equivalent Binary Trees
bool flipEquiv(TreeNode* root1, TreeNode* root2) {
  if (!root1 && !root2) return true;
  else if (root1 && !root2) return false;
  else if (root2 && !root1) return false;
  if (root1->val != root2->val) return false;
  cout << root1->val << " " << root2->val << endl;
  if ((!root1->left && root2->left) || (root1->left && !root2->left) ||
      (!root1->right && root2->right) || (root1->right && !root2->right)) {
    return flipEquiv(root1->left, root2->right) && flipEquiv(root1->right, root2->left);
  }
  if ((root1->left && root2->left) && root1->left->val != root2->left->val)
    return flipEquiv(root1->left, root2->right) && flipEquiv(root1->right, root2->left);
  if ((root1->right && root2->right) && root1->right->val != root2->right->val)
    return flipEquiv(root1->left, root2->right) && flipEquiv(root1->right, root2->left);
  return flipEquiv(root1->left, root2->left) && flipEquiv(root1->right, root2->right);
}

// 284. Peeking Iterator
lass PeekingIterator : public Iterator {
 public:
  bool call_peak = false;
  int val = INT_MIN;
  
	PeekingIterator(const vector<int>& nums) : Iterator(nums) {
	    // Initialize any member here.
	    // **DO NOT** save a copy of nums and manipulate it directly.
	    // You should only use the Iterator interface methods.
	}
  // Returns the next element in the iteration without advancing the iterator.
	int peek() {
    if (call_peak) return val;
    val = next();
    call_peak = true;
    return val;
	}
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	int next() {
	  if (call_peak) {
      call_peak = false;
      return val;
    }  
    return Iterator::next();
	}
	bool hasNext() const {
	  return Iterator::hasNext() || call_peak;  
	}
};
```



```c++
// 840. Magic Squares In Grid
class Solution {
 public:
  bool isMagic(int r_idx, int c_idx, vector<vector<int>>&grid) {
    int same_sum = grid[r_idx-2][c_idx-2]+grid[r_idx-2][c_idx-1]+grid[r_idx-2][c_idx];
    int rec[10] = {0};
    for (int r = r_idx -2; r <= r_idx; ++r) {
      for (int c = c_idx-2; c <= c_idx; ++c) {
        if (grid[r][c] < 1 || grid[r][c] > 9) {
          return false;
        }
        rec[grid[r][c]]++;
      }    
    }
    for (int idx = 1; idx <= 9; ++idx) {
      if (rec[idx]!=1) {
        return false;
      }
    }    
    for (int r = r_idx - 1; r <= r_idx; ++r) {
      int sum_tmp = 0;
      for (int c = c_idx - 2; c <= c_idx; ++c) {
        sum_tmp += grid[r][c];
      }
      if (sum_tmp != same_sum) {
        return false;
      }
    }   
    for (int c = c_idx - 2; c <= c_idx; ++c) {
      int sum_tmp = 0;
      for (int r = r_idx - 2; r <= r_idx; ++r) {
        sum_tmp += grid[r][c];
      }
      if (sum_tmp != same_sum) {
        return false;
      }
    }     
    return (grid[r_idx-2][c_idx-2]+grid[r_idx-1][c_idx-1]+grid[r_idx][c_idx]==same_sum) && (grid[r_idx-2][c_idx]+grid[r_idx-1][c_idx-1]+grid[r_idx][c_idx-2]==same_sum);
  }
  int numMagicSquaresInside(vector<vector<int>>& grid) {
    int row = grid.size();
    int col = grid[0].size();
    int ans = 0;
    for (int r = 2; r < row; ++r) {
      for (int c = 2; c < col; ++c) {
        ans += isMagic(r, c, grid);
      }
    }
    return ans;
  }
};

// 442. Find All Duplicates in an Array
vector<int> findDuplicates(vector<int>& nums) {
  vector<int> ans;
  for (auto num : nums)
    nums[abs(num) - 1] *= -1;
  for (auto num : nums)
    if (nums[abs(num) - 1] > 0) {
      ans.push_back(abs(num));
      nums[abs(num) - 1] *= -1;
  }
  return ans;
}

// 389. Find the Difference
// hash is OK/transform of edit distance.
```





