---
layout: post
title: queue and trie
date: 2020-06-05
categories: [technology]
tags: [tech]
comments: false
---



### queue

**queue:**  

- init with list instead of default deque is fast. queue<int, list<int>>  is better.
- Constructor like: queue<int> que({1,2,3}); list/deque
- operation：empty, size, front（non-const reference）, back, push, pop.
- Non-member: swap, swap two queue.

**deque:**

- Constructor:  deque<int>(4, 5); 4 个 5；deque<int>(iter.begin(), iter.end())
- push_front, push_back, insert, pop_front, pop_back, emplace, emplace_front, emplace_back, front, back, begin, end, r(c)begin, clear, at(boundary check), [], resize, erase
- Empty, size, swap, max_size, shrink_to_fit(an optimization skill), assign
- Non-member: swap

**priority_queue:**

- heap sort 
- Top(const reference), push, emplace, pop, empty, swap, size, swap
- emplace 可以直接调用构造函数，直接传构造函数的参数即可。push先需要构造出对象，多了一次拷贝。
- https://blog.csdn.net/Kprogram/article/details/82055673 
- Non-member: swap
- 常用初始化方法：(默认最大堆，top是最大的元素)

```c++
class mycomparison
{
  bool reverse;
public:
  mycomparison(const bool& revparam=false)
    {reverse=revparam;}
  bool operator() (const int& lhs, const int&rhs) const
  {
    if (reverse) return (lhs>rhs);
    else return (lhs<rhs);
  }
};

int myints[]= {10,60,50,20};

std::priority_queue<int> first;
std::priority_queue<int> second (myints,myints+4);
std::priority_queue<int, std::vector<int>, std::greater<int> >
                          third (myints,myints+4);
// using mycomparison:
typedef std::priority_queue<int,std::vector<int>,mycomparison> mypq_type;

mypq_type fourth;                       // less-than comparison
mypq_type fifth (mycomparison(true));   // greater-than comparison

初始化的时候，如果是自定义的话，需要把这个自定义加上
std::priority_queue<mergeRecord, std::vector<mergeRecord>, mycomparison>
    recsHeap(std::begin(initialRecords), std::end(initialRecords), mycomparison(false,field));
```



```c++
// 621. Task Scheduler
// Method 1: mathematics 
int leastInterval(vector<char>& tasks, int n) {
  int frequency[26] = {0};
  for (const auto task : tasks) {
    ++frequency[task-'A'];
  }
  int max_frequency = 0;
  int max_num = 0;
  for (int i = 0; i < 26; ++i) {
    if (frequency[i] > max_frequency) {
      max_frequency = frequency[i];
      max_num = 1;
    } else if (frequency[i] == max_frequency) {
      ++max_num;
    }
  }
  
  return max(tasks.size(), static_cast<unsigned long>((max_frequency - 1) * (n + 1) + max_num));
}
// Method 2: priority_queue
int leastInterval(vector<char>& tasks, int n) {
  unordered_map<char, int> record;
  for (const auto task : tasks) {
    ++record[task];
  }
  priority_queue<int> status;
  for (const auto [name, frequency] : record) {
    status.push(frequency);
  }
  int ans = 0;
  while (!status.empty()) {
    vector<int> tmp;
    int count = 0;
    for (int i = 0; i <= n; ++i) {
      if (status.empty()) break;
      tmp.push_back(status.top());
      ++count;
      status.pop();
    }
    for (auto& t : tmp) {
      if (--t > 0) {
        status.push(t);
      }
    }
    ans += status.empty() ? count : n + 1;
  }
  return ans;
}


// 622. Design Circular Queue
class MyCircularQueue {
public:
int size_ = 0;
int front_idx_ = -1;
int rear_idx_ = -1;
vector<int> data_;
  
MyCircularQueue(int k) {
  size_ = k;        
  data_.resize(k);
}
    
bool enQueue(int value) {
  if (isFull()) return false;
  
  if (isEmpty()) {
    front_idx_ = rear_idx_ = 0;
  } else {
    ++rear_idx_;
    rear_idx_ %= size_;
  }
  data_[rear_idx_] = value;
  
  return true;
}
    
bool deQueue() {
  if (isEmpty()) return false;
  
  if (front_idx_ == rear_idx_) {
    front_idx_ = rear_idx_ = -1;
  } else {
    front_idx_ = ++front_idx_ % size_;
  }
  
  return true;
}
    
int Front() {
  return front_idx_ == -1? -1:data_[front_idx_];      
}
    
int Rear() {
  return rear_idx_ == -1 ? -1:data_[rear_idx_];      
}
    
bool isEmpty() {
  return front_idx_ < 0 && rear_idx_ < 0;      
}
    
bool isFull() {
  return (front_idx_ - rear_idx_ == 1) || ((rear_idx_ == size_ - 1) && (front_idx_ == 0));      
}
};

// 641. Design Circular Deque
class MyCircularDeque {
public:
  
int size_ = 0;
int count_ = 0;
int front_index_ = -1;
int rear_index_ = -1;  
vector<int> data_;  
  
/** Initialize your data structure here. Set the size of the deque to be k. */
MyCircularDeque(int k) {
  size_ = k;      
  data_.resize(k);
}
    
/** Adds an item at the front of Deque. Return true if the operation is successful. */
bool insertFront(int value) {
  if (isFull()) return false;
  if (isEmpty()) {
    front_index_ = rear_index_ = 0;
  }
  else {
    front_index_ = (front_index_ - 1 + size_) % size_;
  } 
  data_[front_index_] = value;
  ++count_;
  return true;
}
    
/** Adds an item at the rear of Deque. Return true if the operation is successful. */
bool insertLast(int value) {
  if (isFull()) return false;
  if (isEmpty()) {
    rear_index_ = front_index_ = 0; 
  } else {
    rear_index_ = (rear_index_ + 1) % size_;
  }  
  data_[rear_index_] = value;
  ++count_;
  return true;
}
    
/** Deletes an item from the front of Deque. Return true if the operation is successful. */
bool deleteFront() {
  if (isEmpty()) return false;
  front_index_ = (front_index_ + 1) % size_;
  --count_;
  return true;
}
    
/** Deletes an item from the rear of Deque. Return true if the operation is successful. */
bool deleteLast() {
  if (isEmpty()) return false;
  rear_index_ = (rear_index_ - 1 + size_) % size_;
  --count_;
  return true;
}
    
/** Get the front item from the deque. */
int getFront() {
  return isEmpty() ? -1: data_[front_index_];        
}
    
/** Get the last item from the deque. */
int getRear() {
  return isEmpty() ? -1: data_[rear_index_];
}
    
/** Checks whether the circular deque is empty or not. */
bool isEmpty() {
  return !count_;        
}
    
/** Checks whether the circular deque is full or not. */
bool isFull() {
  return count_ == size_;        
}
};

/**
 * Your MyCircularDeque object will be instantiated and called as such:
 * MyCircularDeque* obj = new MyCircularDeque(k);
 * bool param_1 = obj->insertFront(value);
 * bool param_2 = obj->insertLast(value);
 * bool param_3 = obj->deleteFront();
 * bool param_4 = obj->deleteLast();
 * int param_5 = obj->getFront();
 * int param_6 = obj->getRear();
 * bool param_7 = obj->isEmpty();
 * bool param_8 = obj->isFull();
 */

// 353. Design Snake Game
class SnakeGame {
public:
/** Initialize your data structure here.
    @param width - screen width
    @param height - screen height 
    @param food - A list of food positions
    E.g food = [[1,1], [1,0]] means the first food is positioned at [1,1], the second is at [1,0]. */
  
deque<pair<int, int>> snake_;
unordered_map<int, bool> record_;
  
int width_ = 0;
int height_ = 0;
int food_index_ = 0;
vector<vector<int>> food_;  

SnakeGame(int width, int height, vector<vector<int>>& food) {
  width_ = width;
  height_ = height;
  food_ = std::move(food);
  snake_.emplace_back(make_pair(0, 0));
}
    
/** Moves the snake.
    @param direction - 'U' = Up, 'L' = Left, 'R' = Right, 'D' = Down 
    @return The game's score after the move. Return -1 if game over. 
    Game over when snake crosses the screen boundary or bites its body. */
int move(string direction) {
  auto tp = snake_.front();
  
  if (direction == "U") {
    --tp.first;   
  } else if (direction == "D") {
    ++tp.first;
  } else if (direction == "L") {
    --tp.second;
  } else {
    ++tp.second;
  }
  
  if (tp.first < 0 || tp.first >= height_ ||
      tp.second < 0 || tp.second >= width_) {
    return -1;
  }
  
  snake_.emplace_front(tp);
  if (food_index_ < food_.size() && (tp.first == food_[food_index_][0]
      && tp.second == food_[food_index_][1])) {
    ++food_index_;
  } else {
    const auto tmp = snake_.back();
    record_[tmp.first * width_ + tmp.second] = false;
    snake_.pop_back();
    if (record_[tp.first * width_ + tp.second]) {
      return -1;
    }
  }
  record_[tp.first * width_ + tp.second] = true;
  
  return snake_.size() - 1;  
}

};

/**
 * Your SnakeGame object will be instantiated and called as such:
 * SnakeGame* obj = new SnakeGame(width, height, food);
 * int param_1 = obj->move(direction);
 */
```



### Trie

```c++
// 208. Implement Trie (Prefix Tree)
class Trie {
public:

struct Node {
  bool is_word = false;
  Node* child[26] = {nullptr};
};  

unique_ptr<Node> parent_;
  
/** Initialize your data structure here. */
Trie() {
  parent_ = unique_ptr<Node>(new Node());      
}
    
/** Inserts a word into the trie. */
void insert(string word) {
  Node* cur_node = parent_.get();
  int count = 0;
  for (const auto c : word) {
    if (!cur_node->child[c-'a']) {
      cur_node->child[c-'a'] = new Node();
    }
    ++count;
    if (count == word.size()) {
      cur_node->child[c-'a']->is_word = true;
    }  
    cur_node = cur_node->child[c-'a'];
  }
}
    
/** Returns if the word is in the trie. */
bool search(string word) {
  Node* cur_node = parent_.get();
  int count = 0;
  for (const auto c : word) {
    if (!cur_node->child[c-'a']) {
      return false;
    }
    ++count;
    if (count == word.size() && cur_node->child[c-'a']->is_word) return true; 
    cur_node = cur_node->child[c-'a'];
  }
  return false;
}
    
/** Returns if there is any word in the trie that starts with the given prefix. */
bool startsWith(string prefix) {
  Node* cur_node = parent_.get();
  for (const auto c : prefix) {
    if (!cur_node->child[c-'a']) {
      return false;
    }
    cur_node = cur_node->child[c-'a'];
  }
  return true;        
}
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */

// 212. Word Search II
class Solution {

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
  
const int dirs[2][4] = {{-1, 0, 1, 0}, {0, 1, 0, -1}};  
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
  
};
```

