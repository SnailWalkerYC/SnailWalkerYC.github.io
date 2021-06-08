---
layout: post
title: divide and conquer
date: 2021-07-07
categories: [technology]
tags: [tech]
comments: false
---

### Divide and Conquer

```c++
// Merge sort.
// 1. get the separate point. The index.
// 2. recursive call left & right
// 3. merge the sorting with both left & right parts.
void MergeSort(const int l, const int r, std::vector<int>& nums) {
  if (l >= r) return;
  const int mid = l + (r - l)/2;
  MergeSort(l, mid, nums);
  MergeSort(mid+1, r, nums);
  std::vector<int> tmp(r - l + 1);
  int i = l; 
  int j = mid+1;
  int cnt = 0;
  while (i <= mid && j <= r) {
    if (nums[i] <= nums[j]) {
      tmp[cnt++] = nums[i++];
    } else {
      tmp[cnt++] = nums[j++];
    }
  }
  
  while (i <= mid) tmp[cnt++] = nums[i++];
  while (j <= r) tmp[cnt++] = nums[j++];
  for (int i = l, j = 0; i <= r; ++i, ++j) {
    nums[i] = tmp[j];
  }    
}

// inverse order numbers

```







```c++
// 1755. Closest Subsequence Sum
class Solution {
 public:
  void GetSum(const vector<int>& nums, const int target, const int idx, 
              const int cur, unordered_set<int>& s) {
    if (idx > target) {
      s.insert(cur);
      return;
    }
    GetSum(nums, target, idx+1, cur, s);
    GetSum(nums, target, idx+1, cur + nums[idx], s);
  }
  
  int minAbsDifference(vector<int>& nums, int goal) {
    unordered_set<int> sum1;
    unordered_set<int> sum2;
    
    GetSum(nums, nums.size()/2, 0, 0, sum1);
    GetSum(nums, nums.size() - 1, nums.size()/2+1, 0, sum2);
    
    vector<int> half_vec(sum1.begin(), sum1.end());
    sort(half_vec.begin(), half_vec.end());
    
    int min_val = INT_MAX;
    for (const auto& num : sum2) {
      int idx = lower_bound(half_vec.begin(), half_vec.end(), 
                            goal - num) - half_vec.begin(); 
      if (idx < half_vec.size()) 
        min_val = min(min_val, abs(goal - half_vec[idx] - num));
      if (idx > 0)
        min_val = min(min_val, abs(goal - half_vec[idx-1] - num));
    }
    return min_val;
  }
};

// 53. Maximum Subarray
tuple<int, int, int, int> MaxMergeSum(const vector<int>& nums, int left, int right) {
  if (left == right) return make_tuple(nums[left], nums[left], 
                             nums[left], nums[left]);
  
  const int mid = (right - left)/2 + left;
  tuple<int, int, int, int> l_sums = MaxMergeSum(nums, left, mid);
  tuple<int, int, int, int> r_sums = MaxMergeSum(nums, mid+1, right);
  
  const int ttl_sum = get<0>(l_sums) + get<0>(r_sums);
  const int l_max_sum = max(get<1>(l_sums), get<0>(l_sums) + get<1>(r_sums));
  const int r_max_sum = max(get<2>(l_sums)+get<0>(r_sums), get<2>(r_sums));
  const int m_max_sum = max(max(get<3>(l_sums), get<3>(r_sums)), get<2>(l_sums) + get<1>(r_sums));

  return make_tuple(ttl_sum, l_max_sum, r_max_sum, m_max_sum);
}  
  
int maxSubArray(vector<int>& nums) {
  return get<3>(MaxMergeSum(nums, 0, nums.size() - 1));
}

```

