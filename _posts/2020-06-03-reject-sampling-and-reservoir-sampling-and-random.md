---
layout: post
title: reject sampling and reservoir sampling and random
date: 2020-06-03
categories: [technology]
tags: [tech]
comments: false
---

 - 说明
 随机数部分，考察的是比如对一个形状进行均匀采样，或者从随机数A生成B。采样其中一种叫做“reject sampling”，直到采到需要的样本才停止，可以计算其期望。
 
  - Reject Sampling
  
```c++
// 478. Generate Random Point in a Circle
// Trick: uniform [0, 1] with rand()/RAND_MAX. radius needs sqrt.
// Why sqrt? 3.3
double radius_ = 0;
double x_center_ = 0.0;
double y_center_ = 0.0;
const double PI = 3.14159265;
    
Solution(double radius, double x_center, double y_center) {
  radius_ = radius;
  x_center_ = x_center;
  y_center_ = y_center;  
}
    
vector<double> randPoint() {
  const double angle = (double) rand() / RAND_MAX * PI * 2 - PI ;
  const double r = sqrt((double)rand()/RAND_MAX) * radius_;
  const double new_x = x_center_ + r*cos(angle); 
  const double new_y = y_center_ + r*sin(angle);
 
  return {new_x, new_y};  
}

// Another method is using the formula: (x - x')^2 + (y - y')^2 <= radius^2 to verify to find the suitable solution.

// 470. Implement Rand10() Using Rand7()
int rand10() {
  int val = 40;  
  while (val >= 40) {
    val = 7 * (rand7() - 1) + rand7() - 1;  
  }  
  return val % 10 + 1;  
}
``` 

Ref:
 - [Why sqrt?](https://www.cs.cornell.edu/courses/cs6630/2015fa/notes/pdf-transform.pdf )
 - [rand7() to rand10()](https://leetcode.com/problems/implement-rand10-using-rand7/discuss/151567/C%2B%2BJavaPython-1.183-Call-of-rand7-Per-rand10)
 - [distribution](https://www.shangmayuan.com/a/0e43475c1b884f10b5a2afe7.html)
 - [Thought](https://blog.csdn.net/u013630349/article/details/47908633)
 - [More generalization](https://leetcode.com/problems/implement-rand10-using-rand7/discuss/150301/Three-line-Java-solution-the-idea-can-be-generalized-to-%22Implement-RandM()-Using-RandN()%22)

 - Reservoir Sampling
 
```c++
 // 398. Random Pick Index
 
 // Reservoir samping
vector<int> nums_;
Solution(vector<int>& nums) {
  nums_ = nums;
}
    
int pick(int target) {
  int count = 0;
  int idx = 0;
  for (int i = 0; i < nums_.size(); ++i) {
    if (nums_[i] != target) continue;
    if (rand() % (++count) == 0) idx = i;  
  } 
  return idx;  
}
 
 // Cache method
 unordered_map<int, vector<int>> record_;
    
Solution(vector<int>& nums) {
  for (int i = 0; i < nums.size(); ++i) {
    record_[nums[i]].push_back(i);
  }
}
    
int pick(int target) {
  return record_[target][rand() % record_[target].size()]; 
}

// 382. Linked List Random Node
ListNode* head_ = nullptr;
    
/** @param head The linked list's head.
    Note that the head is guaranteed to be not null, so it contains at least one node. */
Solution(ListNode* head) {
  head_ = head;  
}
    
/** Returns a random node's value. */
int getRandom() {
  int count = 0;
  int val = head_->val;
  ListNode* cur = head_;
  while (cur != nullptr) {
    if (rand() % (++count) == 0) val = cur->val; 
    cur = cur->next;  
  } 
  return val;  
}
```

 - Random
 
```
// 528. Random Pick with Weight
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

// 497. Random Point in Non-overlapping Rectangles
vector<double> accumulate_sum_;
vector<vector<int>> rects_;
    
double NumPoints(const vector<int>& rect) {
  return (rect[2] + 1.0 - rect[0]) * (rect[3] + 1.0 - rect[1]);
}
    
Solution(vector<vector<int>>& rects) {
  rects_ = rects;
  for (const auto rect : rects) {
     accumulate_sum_.push_back(NumPoints(rect) + 
                    (accumulate_sum_.empty()?0:accumulate_sum_.back()));
  }  
}
    
vector<int> pick() {
  const double val = static_cast<double>(rand()) / RAND_MAX * accumulate_sum_.back();  
  const int index = min((int)(rects_.size()-1), 
                        (int)(lower_bound(accumulate_sum_.begin(), accumulate_sum_.end(),
                                            val) - begin(accumulate_sum_)));
  const auto rect = rects_[index];
  const double width = (rect[2] - rect[0] + 1.0) * (static_cast<double>(rand()) / RAND_MAX);
  const double height = (rect[3] - rect[1] + 1.0) * (static_cast<double>(rand()) / RAND_MAX);
  return {rect[0] + min(int(floor(width)), rect[2] - rect[0]), rect[1] + min(int(floor(height)), rect[3] - rect[1])};  
}

// 710. Random Pick with Blacklist
unordered_set<int> black_list_;
vector<int> record_;
int n_;
    
Solution(int N, vector<int>& blacklist) {
  n_ = N;
  for (const auto num : blacklist) 
    black_list_.insert(num);
  if (black_list_.size() * 2 > n_) {
    for (int i = 0; i < n_; ++i) {
      if (black_list_.find(i) == black_list_.end())
        record_.push_back(i);  
    }
  }  
}
    
int pick() {
  if (!record_.empty()) {
    const int index = min(floor(static_cast<double>(rand()) / RAND_MAX * record_.size()), record_.size() - 1.0);
    return record_[index];   
  } 
  if (black_list_.empty()) return static_cast<double>(rand()) / RAND_MAX * n_;  
  auto it = black_list_.begin();
  int val = *it;  
  while (black_list_.find(val) != black_list_.end()) {
    val = floor(static_cast<double>(rand()) / RAND_MAX * n_);
  }  
  return val;  
}

// 519. Random Flip Matrix
// Fisher–Yates shuffle: generate a random sequnce
unordered_map<int, int> record_;
int n_rows_;
int n_cols_;
int remain_;
    
Solution(int n_rows, int n_cols) {
  n_rows_ = n_rows;
  n_cols_ = n_cols;  
  remain_ = n_rows * n_cols;  
}
    
vector<int> flip() {
  const int num = min(floor(static_cast<double>(rand()) / RAND_MAX * remain_), remain_ - 1.0);  
  vector<int> res;
  if (record_.find(num) != record_.end()) {
    res.assign({record_[num] / n_cols_, record_[num] % n_cols_});   
  } else {
    res.assign({num/n_cols_, num%n_cols_});
  } 
  record_[num] = record_.find(remain_ - 1) == record_.end()? remain_-1:record_[remain_-1];   
  --remain_;
  return res;  
}
    
void reset() {
  remain_ = n_rows_ * n_cols_;
  record_.clear();  
}
```

Ref:
 - [Sampling iluustration](https://zhuanlan.zhihu.com/p/29178293)