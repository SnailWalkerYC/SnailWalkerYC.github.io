---
layout: post
title: Cruise ai interview preparation
date: 2020-09-01
categories: [technology]
tags: [tech]
comments: false
---



```c++
// 63. Unique Paths II
int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
  if (!obstacleGrid.size() || !obstacleGrid[0].size()) return 0;
  int row = obstacleGrid.size();
  int col = obstacleGrid[0].size();
  vector<int> record(col, 0);
  record[0] = 1 - obstacleGrid[0][0];
  for (int r = 0; r < row; ++r) {
    record[0] &= 1 - obstacleGrid[r][0];
    for (int c = 1; c < col; ++c) {
      if (obstacleGrid[r][c] > 0) {
        record[c] = 0;
        continue;
      }
      record[c] += record[c - 1];
    }
  }
  return record[col - 1];
}

// 980. Unique Paths III

// 986. Interval List Intersections
vector<vector<int>> intervalIntersection(vector<vector<int>>& first, vector<vector<int>>& second) {
  vector<vector<int>> ans;
  int idx1 = 0;
  int idx2 = 0;
  while (idx1 < first.size() && idx2 < second.size()) {
    if (first[idx1][1] < second[idx2][0]) {
      ++idx1;
    } else if (second[idx2][1] < first[idx1][0]) {
      ++idx2;
    } else {
      ans.emplace_back(vector<int>({max(first[idx1][0], second[idx2][0]),
                                    min(first[idx1][1], second[idx2][1])}));
      if (first[idx1][1] > second[idx2][1]) {
        ++idx2;
      } else {
        ++idx1;
      }
    }
  }
  return ans;
}

// 1258. Synonymous Sentences

// 489. Robot Room Cleaner

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

// 735. Asteroid Collision
vector<int> asteroidCollision(vector<int>& asteroids) {
  vector<int> ans;
  stack<int> stk;
  for (const auto& as : asteroids) {
    if (stk.empty()) {
      if (as < 0) {
        ans.emplace_back(as);
      } else {
        stk.push(as);
      }
    } else {
      if (as > 0) {
        stk.push(as);
      } else {
        while (!stk.empty() && stk.top() < abs(as)) {
          stk.pop();
        }
        if (stk.empty())
          ans.emplace_back(as);
        else if (stk.top() == abs(as))
          stk.pop();
      } 
    }
  }
  const int size = ans.size();
  while (!stk.empty()) {
    ans.emplace_back(stk.top());
    stk.pop();
  }
  reverse(ans.begin() + size, ans.end());
  return ans;
}

// 84. Largest Rectangle in Histogram
int largestRectangleArea(vector<int>& heights) {
  stack<int> record;
  heights.insert(heights.begin(), 0);8
  heights.emplace_back(0);
  int max_area = 0;
  record.push(0);
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

// 529. Minesweeper
vector<vector<char>> updateBoard(vector<vector<char>>& board, vector<int>& click) {
  const int dirs[2][8] = { {-1, 0, 1, 1, 1, 0, -1, -1}, {-1, -1, -1, 0, 1, 1, 1, 0} };
  queue<pair<int, int>> processing;
  processing.push(make_pair(click[0], click[1]));
  while (!processing.empty()) {
    const auto [row, col] = processing.front();
    processing.pop();
    if (board[row][col] == 'M') {
      board[row][col] = 'X';
      return board;
    } else if (board[row][col] == 'E') {
      int cnt_mine = 0;
      vector<int> empty;
      for (int i = 0; i < 8; ++i) {
        const int new_row = row + dirs[1][i];
        const int new_col = col + dirs[0][i];
        if (new_row < 0 || new_col < 0 || new_row >= board.size() ||
              new_col >= board[0].size()) continue;
        if (board[new_row][new_col] == 'M') ++cnt_mine;
        else if (board[new_row][new_col] == 'E') 
          empty.push_back(i);
      }
      if (!cnt_mine) {
        board[row][col] = 'B';
        for (const auto& idx : empty) {
          processing.push(make_pair(dirs[1][idx] + row, dirs[0][idx] + col));
        }
      } else {
        board[row][col] = '0' + cnt_mine;
      }  
    }
  }
  return board;
}

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

// 200. Number of Islands
int numIslands(vector<vector<char>>& grid) {
  int row = grid.size();
  int cnt = 0;
  if (!row) {
    return cnt;
  }
  int col = grid[0].size();
  int dir[4][2] = { {-1, 0}, {0, 1}, {1, 0}, {0, -1} };      
  for (int r = 0; r < row; ++r) {
    for (int c = 0; c < col; ++c) {
      if (grid[r][c] < '1') {
        continue;
      }
      queue<int> que; 
      que.push(r*col + c);
      grid[r][c] = -1;
      while (!que.empty()) {
        int cur_r = que.front()/col;
        int cur_c = que.front()%col;
        for (int d = 0; d < 4; ++d) {
          int new_r = cur_r + dir[d][0];
          int new_c = cur_c + dir[d][1];
          if (new_r < 0 || new_r >= row || new_c < 0 || new_c >= col) {
            continue;
          }
          if (grid[new_r][new_c] == '1') {
            grid[new_r][new_c] = '0';
            que.push(new_r*col + new_c);
          }
        }
        que.pop();
      }
      ++cnt;
    }
  } 
  return cnt;
}
```



```c++
// Calculate ACU
// https://towardsdatascience.com/how-to-efficiently-implement-area-under-precision-recall-curve-pr-auc-a85872fd7f14
```



```c++
// Shared_ptr
template <class Type>
class SharedPtr {
 public:
  SharedPtr() : count_ref_(new int(0)) {}
  
  SharedPtr(T* ptr) : data_(ptr), count_ref(new int(1)) {}
  
  // copy
  SharedPtr(const SharedPtr& obj) {
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    if (data_ != nullptr) {
      ++(*count_ref_);
    }
  }
  
  SharedPtr& operator=(const SharedPtr& obj) {
    CleanUp();
    
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    if (data_ != nullptr) {
      ++(*count_ref_);
    }
  }
  
  // move
  SharedPtr(SharedPtr&& obj) {
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    obj.data_ = obj.count_ref_ = nullptr;
  }
  
  SharedPtr& operator=(SharedPtr && obj) {
    CleanUp();
    
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    obj.data_ = obj.count_ref_ = nullptr;
  }
  
  int GetCount() const {
    return *count_ref_;
  }
  
  T& Get() const {
    return data_;
  }
  
  T* operator->() const {
    return data_;
  }
  
  T& operator*() const {
    return data_;
  }
  
  ~SharedPtr() {
    --(*count_ref_);
    if (!*count_ref_) {
      if (data_ != nullptr) delete data_;
      delete count_ref_;
    }
  }
  
 private:
  int* count_ref_ = nullptr;
  T* data_ = nullptr;
};  

// Example
class Box {
 public:
  int length, width, height;
  Box() : length(0), width(0), height(0) {}
};

int main() {
  SharedPtr<Box> obj;
  cout << obj.GetCount() << endl; // prints 0
  SharedPtr<Box> box1(new Box());
  cout << box1.GetCount() << endl; // prints 1
  SharedPtr<Box> box2(box1); // calls copy constructor
  cout << box1.GetCount() << endl; // prints 2
  cout << box2.GetCount() << endl; // also prints 2
 
  return 0;
}
```



```c++
// Memory pool
// http://www.pinksquirrellabs.com/blog/2018/01/31/-fixed-memory-pool-design/
```



```c++
// Polygon in another Polygon
// Verify all points are in another polygon.
```



```c++
// Cruise topic
// https://leetcode.com/company/cruise-automation/

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

// 694. Number of Distinct Islands
bool SameIsland(const vector<int>& cur_traj, const vector<vector<int>>& target) {
  int len = 0;
  queue<pair<int, int>> que;
  for (int i = 0; i < target.size(); ++i) {
    que.push(make_pair(i, 50));
  }
  while (!que.empty()) {
    const int cur_que = que.size();
    bool has_same = false;
    for (int i = 0; i < cur_que; ++i) {
      const auto [idx, pos] = que.front();
      que.pop();
      if (pos == cur_traj[len]) {
        has_same = true;
        que.push(make_pair(idx, len+1 >= cur_traj.size()?-1:target[idx][len+1]));
      }
    }
    if (has_same)
      ++len;
    if (len >= cur_traj.size())
      break;
  }
  return len == cur_traj.size()?true:false;
}  
int numDistinctIslands(vector<vector<int>>& grid) {
  unordered_map<int, vector<vector<int>>> trajectory;
  const int shift = 50;
  const int dirs[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };
  for (int i = 0; i < grid.size(); ++i) {
    for (int j = 0; j < grid[0].size(); ++j) {
      if (grid[i][j]) {
        int num = 0;
        vector<int> cur_traj;
        grid[i][j] = 0;
        queue<pair<int, int>> que({ {i, j} });
        while (!que.empty()) {
          const auto [row, col] = que.front();
          que.pop(); 
          ++num;
          cur_traj.emplace_back((row - i) * 100 + j - col + 50);
          for (int k = 0; k < 4; ++k) {
            const int nr = row + dirs[1][k];
            const int nc = col + dirs[0][k];
            if (nr < 0 || nc < 0 || nr >= grid.size() || 
                nc >= grid[0].size() || !grid[nr][nc]) {
              continue;
            }
            que.push(make_pair(nr, nc));
            grid[nr][nc] = 0;
          }
        }
        if (!trajectory.count(num)) {
          trajectory[num] = {cur_traj};
        } else if (!SameIsland(cur_traj, trajectory[num])) {
          trajectory[num].push_back(cur_traj);
        }
      }
    }
  }
  int ans = 0;
  for (const auto& [size, eles] : trajectory) {
    ans += eles.size();
  }
  return ans;
}

// 695. Max Area of Island
int maxAreaOfIsland(vector<vector<int>>& grid) {
  if (!grid.size() || !grid[0].size()) {
    return 0;
  }
  int row = grid.size();
  int col = grid[0].size();
  priority_queue<int> cur_island; 
  int cnt_res = 0;
  const int MAX_SIZE = 50;
  int dirs[4][2] = { {-1, 0}, {0, 1}, {1, 0}, {0, -1} };
  const int NUM_DIR = 4;
  for (int r = 0; r < row; ++r) {
    for (int c = 0; c < col; ++c) {
      if (!grid[r][c]) {
        continue;
      }
      cur_island.push(r*MAX_SIZE + c);
      grid[r][c] = 0;
      int cnt = 1;
      while (!cur_island.empty()) {
        int tp = cur_island.top();
        cur_island.pop();
        for (int idx = 0; idx < NUM_DIR; ++idx) {
          int cur_r = tp/MAX_SIZE + dirs[idx][0];
          int cur_c = tp%MAX_SIZE + dirs[idx][1];
          if (cur_r < 0 || cur_c < 0 || 
              cur_r >= row || cur_c >= col ||
              !grid[cur_r][cur_c]) {
             continue;
          }
          ++cnt;
          cur_island.push(cur_r*MAX_SIZE + cur_c);
          grid[cur_r][cur_c] = 0;
        }        
      }
      cnt_res = max(cnt_res, cnt);  
    }
  }
  return cnt_res;
}

// 981. Time Based Key-Value Store
class TimeMap {
 public:
  /** Initialize your data structure here. */
  TimeMap() {}
  
  void set(string key, string value, int timestamp) {
    data_[key].emplace_back(timestamp, value);      
  }
    
  string get(string key, int timestamp) {
    const auto& data = data_[key];
    if (data.empty()) return "";
    auto it = upper_bound(begin(data), end(data), 
      make_pair(timestamp, key), [](const pair<int, string>& p1,
                            const pair<int, string>& p2) {
        return p1.first < p2.first;
      });
    if (it != begin(data)) {
      --it;
    } else {
      return "";
    }
    return it->second;
  }
  
 private: 
  unordered_map<string, vector<pair<int, string>>> data_;
};

// 528. Random Pick with Weight
class Solution {
 public:
  vector<int> accumulate_sum_;
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
};

// 79. Word Search
const int dirs[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };
bool Exist(const vector<vector<char>>& board, const int row, 
           const int col, const int index, const string& word,
           vector<vector<bool>>& status) {
  if (index >= word.size()) return true;
  if (row < 0 || row >= board.size() ||
      col < 0 || col >= board[0].size() ||
      board[row][col] != word[index])
    return false;
  if (status[row][col]) return false;
  status[row][col] = true;
  for (int i = 0; i < 4; ++i) {
    if (Exist(board, row + dirs[1][i], col + dirs[0][i], 
          index+1, word, status))
      return true;
  }
  status[row][col] = false;
  return false;
}  
bool exist(vector<vector<char>>& board, string word) {
  for (int i = 0; i < board.size(); ++i) {
    for (int j = 0; j < board[0].size(); ++j) {
      if (board[i][j] == word[0]) {
        vector<vector<bool>> status(board.size(), 
            vector<bool>(board[0].size(), false));
        if (Exist(board, i, j, 0, word, status)) {
          return true;
        }
      }
    }
  }  
  return false;
}

// 1094. Car Pooling
bool carPooling(vector<vector<int>>& trips, int capacity) {
  map<int, vector<int>> record;
  for (const auto& trip : trips) {
    record[trip[1]].emplace_back(trip[0]);
    record[trip[2]].emplace_back(-trip[0]);
  }
  int cur = 0;
  for (const auto& r : record) {
    for (const auto& cap : r.second) {
      cur += cap;
    }
    if (cur > capacity) {
      return false;
    }
  }
  return true;
}

// 304. Range Sum Query 2D - Immutable
class NumMatrix {
 public:   
  vector<vector<int>> _matrix;
  NumMatrix(vector<vector<int>>& matrix) {
    _matrix = matrix;
    int row = matrix.size();
    int col = row?matrix[0].size():0;    
    for (int r = 0; r < row; ++r) {
      for (int c = 1; c < col; ++c) {
        _matrix[r][c] += _matrix[r][c-1];
      }
    }    
    for (int c = 0; c < col; ++c) {
      for (int r = 1; r < row; ++r) {
        _matrix[r][c] += _matrix[r-1][c];
      }
    }
  }  
  int sumRegion(int row1, int col1, int row2, int col2) {
    int s_1 = _matrix[row2][col2];
    int row1_1 = row1 - 1;
    int col1_1 = col1 - 1;
    int row2_1 = row2 - 1;
    int col2_1 = col2 - 1;
    int s_2 = row1_1 >= 0 && col1_1 >= 0?_matrix[row1_1][col1_1]:0;
    int s_3 = row1_1 >= 0?_matrix[row1_1][col2]:0;
    int s_4 = col1_1 >= 0?_matrix[row2][col1_1]:0;
    return s_1 + s_2 - s_3 - s_4;
  }
};

// 36. Valid Sudoku
const int size = 9;
bool insert_record(vector<unordered_set<int>> &rec, int row, int col, char c) {
  int tar = c-'0';
  if(rec[row].find(tar)!=rec[row].end())
    return false;
  rec[row].insert(tar);
  if(rec[col+size].find(tar)!=rec[col+size].end())
    return false;
  rec[col+size].insert(tar);
  int rl=row/3;
  int cl=col/3;
  int blk = size*2 + rl*3 + cl;
  if(rec[blk].find(tar)!=rec[blk].end())
    return false;
  rec[blk].insert(tar);
  return true;
}    
bool isValidSudoku(vector<vector<char>>& board) {
  assert(board.size()==size && board[0].size()==9);
  vector<unordered_set<int>> rec(27,unordered_set<int>());
  for(int row=0; row<size; row++) {
    for(int col=0; col<size; col++) {
      bool verify = true;
      if(board[row][col]!='.')
        verify = insert_record(rec, row, col, board[row][col]);
      if(!verify)
        return false;
    }
  }
  return true;
}
```

