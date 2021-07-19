---
layout: post
title: Baidu interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



```c++
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

// Verify if two line segment intersect
// https://www.cnblogs.com/kane1990/p/5742830.html

// 456. 132 Pattern
bool find132pattern(vector<int>& nums) {
  stack<int> stk;
  int right = INT_MIN;
  for (auto it = nums.rbegin(); it != nums.rend(); ++it) {
    if (*it < right) return true;
    while (!stk.empty() && stk.top() < *it) {
      right = stk.top();
      stk.pop();
    }
    stk.push(*it);
  }
  return false;
}

// 535. Encode and Decode TinyURL
unordered_map<string, string> longToShort;
  unordered_map<string, string> shortToLong;
  int num_codes = 7;
  string codes = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";  
  // Encodes a URL to a shortened URL.
  string encode(string longUrl) {
    bool hasCode = true;
    string shortUrl = "";
    while(hasCode) {
      int c_ind = 62*rand() % 61;
      shortUrl += codes[c_ind];
      if(shortUrl.size()==num_codes) {
        if(shortToLong.find(shortUrl)!=shortToLong.end()) {
          shortUrl = "";
        }    
        else {
          hasCode = false;
        }
      }
    }    
    longToShort[longUrl] = shortUrl;
    shortToLong[shortUrl] = longUrl;
    return shortUrl;
  }
  // Decodes a shortened URL to its original URL.
  string decode(string shortUrl) {
    return shortToLong[shortUrl];
  }
};

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
  
// 91. Decode Ways
int numDecodings(string s) {
  int m = s.length();
  if (m == 0) return 0;
  if (s[0] == '0') return 0;
  vector<int> cnt(m + 1, 0);
  cnt[0] = 1;
  cnt[1] = 1;
  for (int i = 1; i < m; ++i) {
    // check if 0 is the valid num
    if (s[i] == '0') {
      if (!is_valid_num(s[i - 1], s[i])) return 0;
       else cnt[i + 1] = cnt[i - 1];
     } else {
       cnt[i + 1] += cnt[i];
       if (is_valid_num(s[i - 1], s[i])) cnt[i + 1] += cnt[i - 1];
     }
  }
  return cnt[m];
}
bool is_valid_num(char& a, char& b) {
  if (a == '1' || a == '2' && b < '7') return true;
  return false;
}
  
// Implement vector class
// Self implementation of the Vector Class in C++
#include <bits/stdc++.h>
using namespace std;

template <typename T> 
class vectorClass {
 private: 
	// arr is the integer pointer
	// which stores the address of our vector
	T* arr;
	// capacity is the total storage
	// capacity of the vector
	int capacity;
	// current is the number of elements
	// currently present in the vector
	int current;
public:
	// Default constructor to initialise
	// an initial capacity of 1 element and
	// allocating storage using dynamic allocation
	vectorClass() {
		arr = new T[1];
		capacity = 1;
		current = 0;
	}

	// Function to add an element at the last
	void push(T data) {
		// if the number of elements is equal to the
		// capacity, that means we don't have space to
		// accommodate more elements. We need to double the
		// capacity
		if (current == capacity) {
			T* temp = new T[2 * capacity];
			// copying old array elements to new array
			for (int i = 0; i < capacity; i++) {
				temp[i] = arr[i];
			}
			// deleting previous array
			delete[] arr;
			capacity *= 2;
			arr = temp;
		}
		// Inserting data
		arr[current] = data;
		current++;
	}

	// function to add element at any index
	void push(int data, int index) {
		// if index is equal to capacity then this
		// function is same as push defined above
		if (index == capacity)
			push(data);
		else
			arr[index] = data;
	}

	// function to extract element at any index
	T get(int index) {
		// if index is within the range
		if (index < current)
			return arr[index];
	}

	// function to delete last element
	void pop() { current--; }

	// function to get size of the vector
	int size() { return current; }

	// function to get capacity of the vector
	int getcapacity() { return capacity; }

	// function to print array elements
	void print()
	{
		for (int i = 0; i < current; i++) {
			cout << arr[i] << " ";
		}
		cout << endl;
	}
};

// Driver code
int main() {
	vectorClass<int> v;
	vectorClass<char> v1;
	v.push(10);
	v.push(20);
	v.push(30);
	v.push(40);
	v.push(50);
	v1.push(71);
	v1.push(72);
	v1.push(73);
	v1.push(74);

	cout << "Vector size : " << v.size() << endl;
	cout << "Vector capacity : " << v.getcapacity() << endl;
	cout << "Vector elements : ";
	v.print();
	v.push(100, 1);

	cout << "\nAfter updating 1st index" << endl;
	cout << "Vector elements of type int : " << endl;
	v.print();
	// This was possible because we used templates
	cout << "Vector elements of type char : " << endl;
	v1.print();
	cout << "Element at 1st index of type int: " << v.get(1)
		<< endl;
	cout << "Element at 1st index of type char: "
		<< v1.get(1) << endl;

	v.pop();
	v1.pop();

	cout << "\nAfter deleting last element" << endl;
	cout << "Vector size of type int: " << v.size() << endl;
	cout << "Vector size of type char: " << v1.size()
		<< endl;
	cout << "Vector capacity of type int : "
		<< v.getcapacity() << endl;
	cout << "Vector capacity of type char : "
		<< v1.getcapacity() << endl;

	cout << "Vector elements of type int: ";
	v.print();
	cout << "Vector elements of type char: ";
	v1.print();

	return 0;
}  
  
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

// reverse bits in a byte.
uint32_t reverseBits(uint32_t n) {
  uint32_t res = 0;
  int power = 31;
  while (n > 0) {
    res |= (n & 1) << power;
    --power;
    n = n >> 1;
  }
  return res;
} 
// Shift by 16, 8, 4, 2, 1 bits.

// fabonacci number.
// https://www.huaweicloud.com/articles/8359906.html
  
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

// 矩阵迷宫start走到end，中间有路障，和金币，要求吃到所有金币，有多少种走法？backtracking.
  
// 5. Longest Palindromic Substring  
string longestPalindrome(string s) {
  string ans;
  int s_len = s.size();
  if(!s_len)
    return s;
  vector<vector<bool>> rec(s_len, vector<bool>(s_len, 0));
  for(int len = 1; len <= s_len; len++) {
    for(int ind = 0; ind<=s_len-len; ind++) {
      int ind2 = ind+len-1;
      rec[ind][ind2] = s[ind]==s[ind2] && (len==1 || len==2 || rec[ind+1][ind2-1]);
      if(rec[ind][ind2] && ans.size()<len)
        ans = s.substr(ind, len);
    }
  }
  return ans;
}
// Expanding center
string longestPalindrome(string s) {
  int slen = s.length();
  string res;
  if(!slen) return res;      
  int max_cnt = 1;
  res = s[0];      
  for(int i = 0; i < slen; i++) {
    int ind1 = i, ind2 = i;
    int cnt = -1;
    while(1) { 
      if(s[ind1] == s[ind2]) cnt += 2;
      else break;
      ind1--; ind2++;
      if(ind1 < 0 || ind2 >= slen) break;
    }
    if(cnt > max_cnt){
      ind1 = ind1 + 1;
      max_cnt = cnt; res = s.substr(ind1, max_cnt);
    }
  }
  for(int i = 0; i < slen-1; i++) {
    int ind1 = i, ind2 = i+1;
    int cnt = 0;
    while(1) {
      if(s[ind1] == s[ind2]) cnt += 2;
      else break;
      ind1--; ind2++;
      if(ind1 < 0 || ind2 >= slen) break;
    }
    if(cnt > max_cnt) {
      ind1 = ind1 + 1;
      max_cnt = cnt;  res = s.substr(ind1, max_cnt);
    }    
  }
  return res;
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
    
// 74. Search a 2D Matrix
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
  
// https://leetcode.com/problems/max-points-on-a-line/
// 149. Max Points on a Line  
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
// 2 max points
int maxPoints(vector<vector<int>>& points) {
  int res = 0;
  for (const auto& p:points) {
    int ss = 0;
    int vs = 0;
    unordered_map<long double, int> record;
    for (const auto& q: points) {
      if (p == q) ++ss;
      else if (p[0] == q[0]) ++vs;
      else {
        ++record[static_cast<long double>(p[1] - q[1]) / (p[0] - q[0])];
      }
    }
    const auto data = {vs + ss, res};
    res = *max_element(begin(data), end(data));
    for (const auto& [k, t] : record) {
      res = max(res, t + ss);
    }
  }
  return res;
}
  
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
      s_end_x++;
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
  
// 348. Design Tic-Tac-Toe
class TicTacToe {
 private:
  vector<int> rec1_;
  vector<int> rec2_;
  int size_;
  
 public:
  /** Initialize your data structure here. */
  TicTacToe(int n) {
    rec1_ = vector<int>((n<<1)+2,0);
    rec2 = vector<int>((n<<1)+2,0);
    size_ = n;
  }  
  /** Player {player} makes a move at ({row}, {col}).
      @param row The row of the board.
      @param col The column of the board.
      @param player The player, can be either 1 or 2.
      @return The current winning condition, can be either:
                0: No one wins.
                1: Player 1 wins.
                2: Player 2 wins. */
  int move(int row, int col, int player) {
    vector<int> &rec = (player==1)?rec1_:rec2_;
    rec[row]++;
    rec[col+size_]++;
    if(col==row)
      rec[size_<<1]++;
    if(col+row==size_-1)
      rec[(size_<<1) + 1]++;
    if(rec[row]==size_ ||
      rec[col+size_]==size_ ||
      rec[size_<<1] == size_ ||
      rec[(size_<<1) + 1] == size_)
      return player;
    return 0;
  }
};
```



-----------------------



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

