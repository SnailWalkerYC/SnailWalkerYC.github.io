---
layout: post
title: tree binary indexed tree
date: 2021-06-12
categories: [technology]
tags: [tech]
comments: false
---

- 使用情况

对于值变化的固定大小的数组进行求解前缀和以及区间和，以及在构造完整的树状数组过程中，保留某些值用于最后求解。



更新的时间复杂度为：O(log(n))

询问的时间复杂度为：O(log(n))



- 原理

即我们希望构造出一个树，树的每个节点代表了一个区间和，最后由区间和我们可以组合出我们需要的区间和。所以如何出这样的区间和，如何设定这个区间。比如我们求解区间和13，prefix_sum[1:13] (假设从1开始)。那么13的二进制表示为：[00001101] -> 从左往右每次遇到1就进行累加，那么就是[1, 8], [9, 12], [13, 13]。所以我们只需要构造出这个区间和形势即可~ 我们在构造树状数组的过程的时候，每个range sum都是会由一组二进制的range sum相加而来，那么我们计算这些range sum的时候，就可以很方便地想到使用位操作来找到我们更新某个数组中的值会在哪些区间里面。



- 实现

```c++
class BinaryIndexTree {
 public:
  vector<int> nums_;
  
  void Update(int idx, int delta) {
    while (idx < nums_.size()) {
      nums_[idx] += delta;
      idx += idx & (~idx + 1);
    }
  }
  
  int Query(int idx) {
    int res = 0;
    while (idx > 0) {
      res += nums_[idx];
      idx -= idx & (~idx + 1);
    }
    return res;
  }
  
  BinaryIndexTree(int n) {
    if (n <= 0) {
      nums_.clear();
      return;
    }
    nums_.reserve(n+1);
    nums_ = vector<int>(n+1, 0);
  }
}
```



```c++
// 1649. Create Sorted Array through Instructions
#define CreateSortedArray(...) createSortedArray(vector<int>& instructions)

class Solution {
public:
    int CreateSortedArray(vector<int>& instructions) {
      int cost = 0;
      vector<int> record(100001, 0);
      int count = 0;
      constexpr int mod_val = 1.0e9 + 7;
      for (const auto& instruction : instructions) {
        cost = (cost + min(Query(instruction - 1, record), count - Query(instruction, record))) % mod_val;
        ++count;
        Update(instruction, &record);
      }
      return cost;
    }
  
    int Query(const int& value, const vector<int>& record) {
      int cur_idx = value;
      int range_sum = 0;
      while (cur_idx > 0) {
        range_sum += record[cur_idx];
        cur_idx -= cur_idx & (~cur_idx + 1); 
      }
      return range_sum;
    }
  
    void Update(const int &value, vector<int>* record) {
      int cur_idx = value;
      while (cur_idx < record->size()) {
        ++(*record)[cur_idx];
        cur_idx += cur_idx & (~cur_idx + 1);
      }
    }
};
```



```c++
// 307. Range Sum Query - Mutable
class NumArray {
public:
    vector<int> nums_;
    vector<int> init_nums_;
  
    NumArray(vector<int>& nums) {
      if (!nums.size()) return;
      nums_.resize(nums.size() + 1);
      init_nums_.resize(nums.size());
      for (int i = 0; i < nums.size(); ++i) {
        update(i, nums[i]);
      }
      init_nums_ = nums;
    }
    
    void update(int i, int val) {
      const int delta = val -  init_nums_[i];
      init_nums_[i] = val;
      ++i;
      while (i < nums_.size()) {
        nums_[i] += delta;
        i += i & (~i + 1);
      }
    }
  
    int PrefixSum(int i) {
      int res = 0;
      while (i > 0) {
        res += nums_[i];
        i -= i & (~i + 1);
      }
      return res;
    }
  
    int sumRange(int i, int j) {
      return PrefixSum(j+1) - PrefixSum(i);
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * obj->update(i,val);
 * int param_2 = obj->sumRange(i,j);
 */
```



```c++
// 315. Count of Smaller Numbers After Self
#define CountSmaller(...) countSmaller(vector<int>& nums)

class Solution {
public:
    
    vector<int> CountSmaller(vector<int>& nums) {
      if (nums.empty()) return {};
      
      vector<int> record(100001, 0);
      vector<int> ans;
      ans.reserve(nums.size());
      for (int i = nums.size() - 1; i >= 0; --i) {
        Update(nums[i] + 1e4 + 1, &record);
        ans.push_back(Query(nums[i]+1e4, record));
      }

      reverse(ans.begin(), ans.end());
      return ans;
    }
  
    void Update(const int& num, vector<int>* record) {
      int cur_idx = num;
      while (cur_idx < record->size()) {
        ++(*record)[cur_idx];
        cur_idx += cur_idx & (~cur_idx + 1);
      }
    }
  
    int Query(const int& idx, const vector<int>& record) {
      int cur_idx = idx;
      int res = 0;
      while (cur_idx > 0) {
        res += record[cur_idx];
        cur_idx -= cur_idx & (~cur_idx + 1);
      }
      return res;
    }
};
```

```c++
// 327. Count of Range Sum
vector<long long> accumulate_nums_;
    
void Update(int idx, vector<int>& record) {
  while (idx < record.size()) {
    ++record[idx];
    idx += idx & (~idx + 1);  
  }
}
    
int Query(int idx, const vector<int>& record) {
  int val = 0;
  while (idx > 0) {
    val += record[idx];
    idx -= idx & (~idx + 1);  
  }  
  return val;  
}
    
int countRangeSum(vector<int>& nums, int lower, int upper) {
  if (!nums.size()) return 0;
        
  accumulate_nums_ = vector<long long>(nums.size()+1, 0);
  for (int i = 0; i < nums.size(); ++i) 
    accumulate_nums_[i+1] = accumulate_nums_[i] + nums[i];
  vector<long long> sort_nums = accumulate_nums_;
  sort(sort_nums.begin(), sort_nums.end());
  vector<int> record(nums.size()+2, 0);
  int val = 0;  
  const long long lower_num = lower;
  const long long upper_num = upper;  
  for (int i = 0; i < accumulate_nums_.size(); ++i) {
    const long long lb_num = accumulate_nums_[i] - upper_num;
    const long long up_num = accumulate_nums_[i] - lower_num;
    const auto it1 = lower_bound(sort_nums.begin(), sort_nums.end(), lb_num);  
    const auto it2 = upper_bound(sort_nums.begin(), sort_nums.end(), up_num); 
    if (it1 != sort_nums.end()) {
      const int idx1 = it1 - sort_nums.begin();
      const int idx2 = (it2 == sort_nums.end()) ? record.size() - 1 : it2 - sort_nums.begin();
      val += Query(idx2, record) - Query(idx1, record); 
    }  
         
    Update(lower_bound(sort_nums.begin(), sort_nums.end(), accumulate_nums_[i]) - sort_nums.begin()+1, record);    
  }  
  return val;  
}
```

```c++
// 493. Reverse Pairs
void Update(int idx, vector<int>& record) {
  while (idx < record.size()) {
    ++record[idx];
    idx += idx & (~idx + 1);  
  }
}
    
int Query(int idx, const vector<int>& record) {
  int val = 0;
  while (idx > 0) {
    val += record[idx];
    idx -= idx & (~idx + 1);  
  }  
  return val;  
}
    
int reversePairs(vector<int>& nums) {
  if (!nums.size())  return 0;
  vector<int> nums_sort = nums;
  sort(nums_sort.begin(), nums_sort.end());
  vector<int> record(nums.size()+1, 0);
  int val = 0;
  for (int i = 0; i < nums.size(); ++i) {
  const auto it = upper_bound(nums_sort.begin(), nums_sort.end(), nums[i]*2LL);
    if (it != nums_sort.end()) {
      val += Query(nums.size(), record) - Query(it - nums_sort.begin(), record);    
    }    
    Update(lower_bound(nums_sort.begin(), nums_sort.end(), nums[i]) - nums_sort.begin() + 1, record);    
  }  
  return val;  
}
```

```c++
// 308. Range Sum Query 2D - Mutable
vector<vector<int>> matrix_;
vector<vector<int>> init_;
    
NumMatrix(vector<vector<int>>& matrix) {
  if (matrix.empty() || matrix[0].empty()) return;  
  matrix_ = vector<vector<int>>(matrix.size() + 1, 
                                    vector<int>(matrix[0].size() + 1));
  init_ = vector<vector<int>>(matrix.size(), vector<int>(matrix[0].size(), 0));
  for (int i = 0; i < matrix.size(); ++i) {
    for (int j = 0; j < matrix[0].size(); ++j) {
      update(i, j, matrix[i][j]);
    }
  }  
  init_ = matrix;    
}
    
void Update(int row, int col, int val) {
  const int delta = val - init_[row][col];
  init_[row][col] = val;
  ++row;
  ++col;  
  for (int r = row; r < matrix_.size(); r += r & (~r + 1)) {
    for (int c = col; c < matrix_[0].size(); c += c & (~c + 1)) {
      matrix_[r][c] += delta;;
    }
  }  
}
    
int Query(int row, int col) {
  int val = 0;  
  for (int r = row; r > 0; r -= r & (~r + 1)) {
    for (int c = col; c > 0; c -= c & (~c + 1)) {
      val += matrix_[r][c];
    }
  }
  return val;  
}
    
int SumRegion(int row1, int col1, int row2, int col2) {
  const int val1 = Query(row1, col1);
  const int val2 = Query(row1, col2+1);
  const int val3 = Query(row2+1, col1);
  const int val4 = Query(row2+1, col2+1);
      
  return val4 + val1 - val2 - val3;  
}
```

- 参考资料：

[花花酱 Fenwick Tree / Binary Indexed Tree](https://www.youtube.com/watch?v=WbafSgetDDk)

[Tutorial: Binary Indexed Tree](https://www.youtube.com/watch?v=v_wj_mOAlig)

[树状数组](https://blog.csdn.net/Yaokai_AssultMaster/article/details/79492190)

[Leetcode binary indexed tree](https://leetcode.com/problems/create-sorted-array-through-instructions/discuss/927531/JavaC%2B%2BPython-Binary-Indexed-Tree)

[Excellent Exploration](https://leetcode.com/problems/range-sum-query-2d-mutable/solution/)