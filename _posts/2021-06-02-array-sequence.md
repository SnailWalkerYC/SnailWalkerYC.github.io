---
layout: post
title: array-sequence
date: 2021-06-02
categories: [technology]
tags: [tech]
comments: false

---



### 逆序对

```c++
int MergeSort(const int left, const int right, vector<int>& nums) {
  if (left >= right) return 0;
  const int mid = left + (right - left) / 2;
  const int left_count = MergeSort(left, mid, nums);
  const int right_count = MergeSort(mid + 1, right, nums);
  vector<int> tmp;
  tmp.reserve(right - left + 1);
  int left_idx = left;
  int right_idx = mid + 1;
  int count_ttl = left_count + right_count;
  while (left_idx <= mid && right_idx <= right) {
    if (nums[left_idx] <= nums[right_idx]) {
      tmp.push_back(nums[left_idx]);
      ++left_idx;
    } else {
      tmp.push_back(nums[right_idx]);
      ++right_idx;
      count_ttl += mid - left_idx + 1;
    }
  }
  while (left_idx <= mid) {
    tmp.push_back(nums[left_idx++]);
  }
  while (right_idx <= right) {
    tmp.push_back(nums[right_idx++]);
  }
  for (int i = left, j = 0; i <= right; ++i, ++j) {
    nums[i] = tmp[j];
  }
  return count_ttl;
}   

// 493
int MergeSort(const int left, const int right, vector<int>& nums) {
  if (left >= right) return 0;
  const int mid = left + (right - left) / 2;
  const int left_count = MergeSort(left, mid, nums);
  const int right_count = MergeSort(mid + 1, right, nums);
  vector<int> tmp;
  int left_idx = left;
  int right_idx = mid + 1;
  int count_ttl = left_count + right_count;
  
  while (left_idx <= mid && right_idx <= right) {
    if (nums[left_idx] <= 2 * static_cast<long long>(nums[right_idx])) {
      ++left_idx;
    } else {
      ++right_idx;
      count_ttl += mid - left_idx + 1;
    }
  }
  
  left_idx = left;
  right_idx = mid + 1;
  while (left_idx <= mid && right_idx <= right) {
    if (nums[left_idx] <= nums[right_idx]) {
      tmp.push_back(nums[left_idx]);
      ++left_idx;
    } else {
      tmp.push_back(nums[right_idx]);
      ++right_idx;
    }
  }
  while (left_idx <= mid) {
    tmp.push_back(nums[left_idx++]);
  }
  while (right_idx <= right) {
    tmp.push_back(nums[right_idx++]);
  }
  for (int i = left, j = 0; i <= right; ++i, ++j) {
    nums[i] = tmp[j];
  }
  return count_ttl;
}  

// 1850. Minimum Adjacent Swaps to Reach the Kth Smallest Number
int getMinSwaps(string num, int k) {
  const string init_num = num;
  while (k-- > 0) next_permutation(num.begin(), num.end());
  vector<int> indexs(num.size(), 0);
  vector<int> count(10, 0);
  for (int i = 0; i < num.size(); ++i) {
    const int cur = init_num[i] - '0';
    ++count[cur];
    int k = 0;
    for (int j = 0; j < num.size(); ++j) {
      if (num[j] == init_num[i]) ++k;
      if (k == count[cur]) {
        indexs[i] = j;
        break;
      }
    }
  }
  int res = 0;
  for (int i = 0; i < num.size(); ++i) {
    for (int j = i + 1; j < num.size(); ++j) {
      if (indexs[i] > indexs[j]) ++res;
    }
  }
  return res;
}
```



### Next Permutation

```c++
// 31. Next Permutation
// in algorithm
void nextPermutation(vector<int>& nums) {
  next_permutation(nums.begin(), nums.end());
}

void nextPermutation(vector<int>& nums) {
  int index = nums.size() - 1;
  while (index - 1 >= 0 && nums[index - 1] >= nums[index]) 
    --index;
  --index;
  if (index < 0) {
    reverse(nums.begin(), nums.end());
    return;
  }
  int k = index + 1;
  while (k < nums.size() && nums[k] > nums[index]) 
    ++k;
  --k;
  swap(nums[k], nums[index]);
  reverse(nums.begin() + index + 1, nums.end());
}

// 60. Permutation Sequence
string getPermutation(int n, int k) {
  vector<int> nums(n, 0);
  for (int i = 1; i <= n; ++i)
    nums[i-1] = i;
  while (--k > 0) {
    next_permutation(nums.begin(), nums.end());
  }
  string ans;
  for (const auto& num : nums) {
    ans += to_string(num);
  }
  return ans;
}
```





Some tricks: prefix sum -> prefix porduct



### k elements

```c++
// 1352. Product of the Last K Numbers
class ProductOfNumbers {
 private:
  vector<int> record_;
  
 public:
  ProductOfNumbers() {
    record_.reserve(40000);
    record_ = {1};
  }
    
  void add(int num) {
    if (!num) {
      record_ = {1};
    } else {
      record_.emplace_back(record_.size() ? record_.back() * num : num);
    }
  }
    
  int getProduct(int k) {
    return record_.size() > k? record_.back() / record_[record_.size() - k - 1] : 0;      
  }
};
```

