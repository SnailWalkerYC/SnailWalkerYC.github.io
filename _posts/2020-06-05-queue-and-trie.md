---
layout: post
title: queue and trie
date: 2020-06-05
categories: [technology]
tags: [tech]
comments: false
---



# queue

**queue:**  

- init with list instead of default deque is fast. $queue<int, list<int>>$ is better.
- Constructor like: $queue<int> que({1,2,3}); list/deque$
- operation：empty, size, front（non-const reference）, back, push, pop.
- Non-member: swap, swap two queue.

**deque:**

- Constructor:  $deque<int>(4, 5); 4 个 5；$ $deque<int>(iter.begin(), iter.end())$ 
- push_front, push_back, insert, pop_front, pop_back, emplace, emplace_front, emplace_back, front, back, begin, end, r(c)begin, clear, at(boundary check), $[]$, resize, erase
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
```

