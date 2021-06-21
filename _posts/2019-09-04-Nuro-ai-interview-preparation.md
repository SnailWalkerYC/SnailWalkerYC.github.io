---

layout: post
title: Nuro ai interview preparation
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false
---



```c++
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
```



```c++
// 1570. Dot Product of Two Sparse Vectors
class SparseVector {
 private:  
  unordered_map<int, int> record_; 
  
 public:
  SparseVector(vector<int> &nums) {
    for (int i = 0; i < nums.size(); ++i) {
      if (nums[i]) {
        record_[i] = nums[i];
      }
    }     
  }
    
  // Return the dotProduct of two sparse vectors
  int dotProduct(SparseVector& vec) {
    int res = 0;
    const auto& num1 = vec.record_.size() >= record_.size()? record_ : vec.record_;
    auto& num2 = vec.record_.size() >= record_.size()? vec.record_ : record_;
    for (const auto& [idx, val] : num1) {
      res += num2[idx] *val;
    }
    return res;
  }
};
```



```c++
// 239. Sliding Window Maximum
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
  deque<int> cur_max;
  vector<int> max_arrs;
  for (int i = 0; i < nums.size(); ++i) {
    while (!cur_max.empty() && nums[i] >= nums[cur_max.front()]) {
      cur_max.pop_front();
    }
    cur_max.push_front(i);
    while (cur_max.back() + k - 1 < i) cur_max.pop_back();
    if (i >= k - 1) {
      max_arrs.emplace_back(nums[cur_max.back()]);
    }
  }
  
  return max_arrs;
}

// 480. Sliding window median
// Insert to left, then transfer left largest to right, compare size for transfering.
vector<double> medianSlidingWindow(vector<int>& nums, int k) {
  multiset<int> left;
  multiset<int> right;
  vector<double> ans;
  
  for (int i = 0; i < nums.size(); ++i) {
    if (i >= k) {
      if (nums[i - k] <= *left.rbegin()) 
        left.erase(left.find(nums[i-k]));
      else
        right.erase(right.find(nums[i-k]));
    }
    left.insert(nums[i]);
    right.insert(*left.rbegin());
    left.erase(prev(left.end()));
    
    if (left.size() < right.size()) {
      left.insert(*right.begin());
      right.erase(right.begin());
    }
    if (i >= k-1) {
      ans.emplace_back(k&1?*left.rbegin():*left.rbegin() * 0.5 + *right.begin()*0.5);
    }
  }
  
  
  return ans;
}

// Sliding window mode is the LFU cache, think about this.
// https://stackoverflow.com/questions/43825229/maximum-absolute-difference-in-array-within-time-window
```



```c++
// 432. All O`one Data Structure
// Method 1: list
class AllOne {
 private:
  struct Node {
    int count = 0;
    unordered_set<string> keys;
    Node(const int cnt, const string& key) {
      count = cnt;
      keys.insert(key);
    }
  };
  
  list<Node> data_;
  unordered_map<string, list<Node>::iterator> record_;
  
 public:  
  /** Initialize your data structure here. */
  AllOne() {}
    
  /** Inserts a new key <Key> with value 1. Or increments an existing key by 1. */
  void inc(string key) {
    if (record_.find(key) == record_.end()) {
      auto it = data_.insert(data_.begin(), Node(0, key));
      record_[key] = it;
    }
    
    auto next = record_[key];
    auto cur = next++;
    if (next == data_.end() || next->count > cur->count + 1) {
      next = data_.insert(next, Node(cur->count+1, key));
    }
    next->keys.insert(key);
    cur->keys.erase(key);
    if (cur->keys.empty()) {
      data_.erase(cur);
    }
    record_[key] = next;
  }
    
  /** Decrements an existing key by 1. If Key's value is 1, remove it from the data structure. */
  void dec(string key) {
    if (record_.find(key) == record_.end())
      return;
    
    auto prev = record_[key];
    auto cur = prev--;
    if (cur == data_.begin() || prev->count + 1 < cur->count) {
      prev = data_.insert(cur, Node(cur->count-1, key));
    }
    prev->keys.insert(key);
    cur->keys.erase(key);
    if (cur->keys.empty()) {
      data_.erase(cur);
      record_.erase(key);
    }
    if (prev->count <= 0) {
      data_.erase(prev);
      record_.erase(key);
    } else {
      record_[key] = prev;
    }
  }
    
  /** Returns one of the keys with maximal value. */
  string getMaxKey() {
    return data_.size() > 0? *(data_.back().keys.begin()) :  "";
  }
    
  /** Returns one of the keys with Minimal value. */
  string getMinKey() {
     return data_.size() > 0? *(data_.front().keys.begin()) :  "";
  }
};

// Method 2: map
class AllOne {
 public:
  map<int, unordered_set<string>> record_;
  unordered_map<string, int> data_;
    
  /** Initialize your data structure here. */
  AllOne() {}
    
  /** Inserts a new key <Key> with value 1. Or increments an existing key by 1. */
  void inc(string key) {
    if (data_.find(key) != data_.end()) {
      record_[data_[key]].erase(key);
      if (!record_[data_[key]].size())
        record_.erase(data_[key]);
    }
    ++data_[key];
    record_[data_[key]].insert(key);
  }
    
  /** Decrements an existing key by 1. If Key's value is 1, remove it from the data structure. */
  void dec(string key) {
    if (data_.find(key) == data_.end())
      return;
    const int cnt = data_[key];
    --data_[key];
    if (!data_[key]) {
      data_.erase(key);
    }
    record_[cnt].erase(key);
    if (!record_[cnt].size()) {
      record_.erase(cnt);
    }
    if (data_.find(key) != data_.end())
      record_[cnt-1].insert(key);
  }
    
  /** Returns one of the keys with maximal value. */
  string getMaxKey() {
    if (!record_.size()) return "";
    return *(record_.crbegin()->second.begin());    
  }
    
  /** Returns one of the keys with Minimal value. */
  string getMinKey() {
    if (!record_.size()) return "";
    return *(record_.cbegin()->second.begin());        
  }
};
```



```c++
// LFU & LRU cache
// 146. LRU Cache
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

// 460. LFU Cache
// Method 1
class LFUCache {
 private:
  struct Node {
    int count_ = 0;
    int value_ = 0;
    int key_ = 0;
    Node(const int cnt, const int v, const int k):
     count_(cnt), value_(v), key_(k) {}
  };
  
  unordered_map<int, list<Node>> data_;
  unordered_map<int, list<Node>::iterator> keys_;
  int min_count_ = INT_MAX;
  int capacity_ = 0;
  
 public:  
  LFUCache(int capacity) {
    capacity_ = capacity;
  }
    
  int get(int key) {
    if (keys_.find(key) == keys_.end())
      return -1;
    const auto it = keys_[key];
    const int prev_count = it->count_;
    const int value = it->value_;
    
    data_[prev_count].erase(it);
    if (data_[prev_count].empty()) {
      data_.erase(prev_count);
      if (min_count_ == prev_count)
        ++min_count_;
    }
    
    auto& cur_level = data_[prev_count+1];
    cur_level.push_back(Node(prev_count+1, value, key));
    keys_[key] = prev(cur_level.end());
    
    return value;
  }
    
  void put(int key, int value) {
    if (!capacity_)
      return;
    if (keys_.find(key) == keys_.end()) {
      if (keys_.size() >= capacity_) {
        while (data_.find(min_count_) == data_.end())
          ++min_count_;
        auto& level = data_[min_count_];
        const int key = level.front().key_;
        keys_.erase(key);
        level.erase(level.begin());
        if (level.empty()) {
          data_.erase(min_count_);
        }
      }
      data_[0].push_back(Node(0, value, key));
      keys_[key] = data_[0].begin();
      min_count_ = 0;
    }
    auto& it = keys_[key];
    const int prev_count = it->count_;
    data_[prev_count].erase(it);
    if (data_[prev_count].empty()) {
      data_.erase(prev_count);
      if (min_count_ == prev_count)
        ++min_count_;
    }
    auto& cur_level = data_[prev_count+1];
    cur_level.push_back(Node(prev_count+1, value, key));
    keys_[key] = prev(cur_level.end());
    
  }
};

// Method 2
class LFUCache {
public:
    class VALUE{
        public:
        int _value;
        int _cnt;
        list<int>::iterator _listItr;
        VALUE(int v, int c, list<int>::iterator list_itr) {
            _value = v;
            _cnt = c;
            _listItr = list_itr;
        }
    };
    
    int _capacity;
    int _minCnt;
    unordered_map<int, VALUE*> _recKey;
    unordered_map<int, list<int>> _hirarchCache;
    
    LFUCache(int capacity) {
        _capacity = capacity;
        _minCnt = 0;
    }
    
    int get(int key) {
        if (_recKey.find(key) == _recKey.end() || _capacity <= 0) {
            return -1;
        }
        
        auto cur_node = _recKey[key];
        _hirarchCache[cur_node->_cnt].erase(cur_node->_listItr);
        if (_minCnt == cur_node->_cnt && !_hirarchCache[cur_node->_cnt].size()) {
            ++_minCnt;
        }
        cur_node->_cnt++;
        _hirarchCache[cur_node->_cnt].push_front(key);
        _recKey[key] = new VALUE(cur_node->_value, cur_node->_cnt, _hirarchCache[cur_node->_cnt].begin());
        
        return cur_node->_value;
    }
    
    void put(int key, int value) {
        if (_capacity <= 0) {
            return;
        }
        if (_recKey.find(key) == _recKey.end() && _recKey.size() < _capacity) {
            _hirarchCache[1].push_front(key);
           VALUE* new_value = new VALUE(
                value,
                1,
                _hirarchCache[1].begin()
            );
            _recKey.insert(make_pair(key, new_value));
            _minCnt = 1;
            return;
        }
        
        if (_recKey.find(key) != _recKey.end()) {
            auto cur_node = _recKey[key];
            _hirarchCache[cur_node->_cnt].erase(cur_node->_listItr);
            if (_minCnt == cur_node->_cnt && !_hirarchCache[cur_node->_cnt].size()) {
                ++_minCnt;
            }
            cur_node->_cnt++;
            _hirarchCache[cur_node->_cnt].push_front(key);
            _recKey[key] = new VALUE(
                value, 
                cur_node->_cnt,                       
                _hirarchCache[cur_node->_cnt].begin());
            return;
        }
        
        auto cur_layer = _hirarchCache[_minCnt];
        int rm_key = cur_layer.back();
        
        _hirarchCache[_minCnt].pop_back();
        _recKey.erase(rm_key);
        _minCnt = 1;
        _hirarchCache[1].push_front(key);
        _recKey[key] = new VALUE(
               value,
               1,
               _hirarchCache[1].begin()
        );
    }
};
```



```c++
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
```



```c++
// 1230. Toss Strange Coins
double probabilityOfHeads(vector<double>& prob, int target) {
  vector<double> join_probs(prob.size()+1, 0.0);
  join_probs[0] = 1.0;
  for (int i = 0; i < prob.size(); ++i) {
    vector<double> tmp = join_probs;
    for (int j = 0; j <= i + 1; ++j) {
      join_probs[j] = tmp[j] * (1.0 - prob[i]) + (j > 0? tmp[j-1] * prob[i]:0.0);
    }
  }
  return join_probs[target];
}
```



```c++
// 817. Linked List Components
int numComponents(ListNode* head, vector<int>& nums) {
  unordered_set<int> record(begin(nums), end(nums));
  int num = 0;
  bool has_last = false;
    
  while (head) {
    if (record.count(head->val) > 0) {
      if (!has_last) {
        has_last = true;
        ++num;
      }
    } else {
      has_last = false;
    }
    head = head->next;
  }
    
  return num;
}
```



```c++
// 273. Integer to English Words
vector<string> one{"", "One", "Two", "Three", "Four", "Five",
                    "Six", "Seven", "Eight", "Nine", "Ten"};
vector<string> two_1{"Eleven", "Twelve", "Thirteen",
                   "Fourteen", "Fifteen", "Sixteen",
                   "Seventeen", "Eighteen", "Nineteen"};
vector<string> two_2{"", "", "Twenty", "Thirty", "Forty",
                  "Fifty", "Sixty", "Seventy",
                  "Eighty", "Ninety"}; 
vector<string> large{"", "", "Hundred", "Thousand", "Million", "Billion"};
  
string numberToWords(int num) {
  if (!num) return "Zero";
  
  int two = num % 100;
  num /= 100;
  string ans;
  if (two <= 10) {
    ans = one[two];
  } else {
    if (two < 20) {
      ans = two_1[two - 11];
    } else {
      const int low = two%10;
      const int high = two/10;
      string add_space = low?" ":"";
      ans = two_2[high] + add_space + one[low];
    }
  }
  string unit = large[2];
  if (num > 0) {
    const int low = num % 10;
    num = num/10;
    string add_space = ans.empty()?"":" ";
    ans = (low > 0?one[low] + " " + unit + add_space: "") + ans;
  }
  
  for (int i = 3; i <= 5; ++i) {
    unit = large[i];
    if (num > 0) {
      const int low = num % 1000;
      num /= 1000;
      const auto res = low > 0?numberToWords(low):"";
      if (!res.empty()) {
        string add_space = ans.empty()?"":" ";
        ans = res + " " + unit + add_space + ans;
      }
    }
  }
    
  return ans;
}
```



```c++
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
```





```c++
// String add
// 415. Add Strings & decresing & multiply
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
```





```c++
// 394. Decode String
string Repeat(const string& str, const int num) {
  if (num == 1) return str;
  if (!num) return "";
  
  return Repeat(str, num/2) + Repeat(str, num/2) + (num&1?str:"");
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
```



```c++
/*
题目是parse yaml file 和 implement query method，每个indentation是两个空格

"""
k1:v1
k2:
  k21:v21
  k22:v22
  k23:
    k231:v231
  k24:v24
k3:
  k31:v31
"""

query("k1") => "v1"
query("k2.k23.k231") => "v231"
*/
class YamlFile {
 private:
  std::unordered_map<std::string, std::unordered_set<std::string>> graph_;
  std::unordered_map<std::string, std::string> values_;
  std::string start_ = "";
  
 public:
  int Parse(std::vector<std::vector<std::string>>& yaml_strs, 
             const std::string& parent = start, const int cur_space = 0,
             const int cur_index = 0) {
    for (int i = cur_index; i < yaml_strs.size(); ) {
      bool has_value = false;
      string key;
      string value;
      const int new_space = CountStatus(yaml_strs[i], &has_value, &key, &value);
      if (new_space == cur_space + 2) {
        graph_[parent].insert(key);
        if (has_value) {
          values_[key] = value;
          ++i;
        } else {
          i = Parse(yaml_strs, yaml_strs[i], new_space, i + 1);  
        }
      } else return i;
    }
    
    return yaml_strs.size();
  }
  
  std::string Query(const std::string& query) {
    const std::vector<std::string> path = Split(query);
    std::string cur = path[0];
    for (int i = 1; i < path.size(); ++i) {
      if (graph_.count(cur) > 0 && graph_[cur].count(path[i]) > 0) {
        cur = path[i];
      } else {
        return "";
      }
    }
    return values_.count(cur) > 0?values_[cur]:"";
  }
};
```

