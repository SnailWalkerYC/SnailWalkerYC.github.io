---
layout: post
title: binary search
date: 2020-05-30
categories: [technology]
tags: [coding]
comments: false
---



- Basic template: stt + 1; stt + (fin - stt)/2; get result from stt and fin.

```c++

// 704. Binary Search 
int search(vector<int>& nums, int target) { 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  int mid_idx; 
  while (stt_idx + 1 < fin_idx) { 
    mid_idx = stt_idx + (fin_idx - stt_idx)/2; 
    if (nums[mid_idx] < target) stt_idx = mid_idx; 
    else if (nums[mid_idx] >= target) fin_idx = mid_idx; 
  } 
  if (nums[stt_idx] == target) return stt_idx; 
  if (nums[fin_idx] == target) return fin_idx; 
  return -1; 
} 
 
// 278. First Bad Version 
int firstBadVersion(int n) { 
  int stt_idx = 1; 
  int fin_idx = n; 
  while (stt_idx + 1 < fin_idx) { 
    int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    if (isBadVersion(mid_idx)) fin_idx = mid_idx; 
    else stt_idx = mid_idx; 
  } 
  if (isBadVersion(stt_idx)) return stt_idx; 
  if (isBadVersion(fin_idx)) return fin_idx; 
  return -1; 
} 
```

- **Search for range** 

```c++
// 34. Find First and Last Position of Element in Sorted Array 
int BinarySearch(vector<int>& nums, int target, bool status) { 
  if (!nums.size()) return -1; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  int mid = -1; 
  while (stt_idx + 1 < fin_idx) { 
    mid = stt_idx + (fin_idx - stt_idx) / 2; 
    if (nums[mid] > target) fin_idx = mid; 
    else if (nums[mid] < target) stt_idx = mid; 
    else if (!status) fin_idx = mid; 
    else stt_idx = mid; 
  } 
  bool stt_status = nums[stt_idx] == target; 
  bool fin_status = nums[fin_idx] == target; 
  if (!status) { 
    if (stt_status) return stt_idx; 
    if (fin_status) return fin_idx;   
  } else { 
    if (fin_status) return fin_idx; 
    if (stt_status) return stt_idx; 
  } 
  return -1; 
} 

vector<int> searchRange(vector<int>& nums, int target) { 
  int left_idx = BinarySearch(nums, target, 0); 
  int right_idx = BinarySearch(nums, target, 1); 
  return {left_idx, right_idx}; 
} 
```

- **Search for positions**

```c++
// 35. Search Insert Position 
int searchInsert(vector<int>& nums, int target) { 
  if (!nums.size()) return 0; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    int mid = stt_idx + (fin_idx - stt_idx) / 2; 
    if (nums[mid] < target) stt_idx = mid; 
    else fin_idx = mid; 
  } 
  if (nums[stt_idx] == target) return stt_idx; 
  if (nums[fin_idx] == target) return fin_idx; 
  if (nums[stt_idx] > target) return stt_idx; 
  else if (nums[fin_idx] < target) return fin_idx + 1; 
  else return stt_idx + 1; 
} 
```

- **Search in matrix** 

```c++
// 240. Search a 2D Matrix II 
bool searchMatrix(vector<vector<int>>& matrix, int target) { 
  int rows = matrix.size(); 
  int cols = !rows?0:matrix[0].size(); 
  if (!rows || !cols) return false; 
  pair<int, int> search_idx(rows - 1, 0); 
  while (search_idx.first >= 0 && search_idx.second < cols) { 
    const auto [row_idx, col_idx] = search_idx; 
    const auto cur_val = matrix[row_idx][col_idx]; 
    if (cur_val > target) --search_idx.first; 
    else if (cur_val < target) ++search_idx.second; 
    else return true; 
  } 
  return false; 
} 

// 74. Search a 2D Matrix 
pair<int, int> BinarySearch(vector<int>& nums, int target) { 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    const auto val = nums[mid_idx]; 
    if (val < target) stt_idx = mid_idx; 
    else if (val >= target) fin_idx = mid_idx; 
  } 
  if (nums[stt_idx] == target || nums[fin_idx] == target) return {0, true}; 
  if (nums[stt_idx] > target) return {-1, false}; 
  if (nums[fin_idx] < target) return {fin_idx, false}; 
  return {stt_idx, false}; 
} 

bool searchMatrix(vector<vector<int>>& matrix, int target) { 
  int rows = matrix.size(); 
  int cols = !rows?0:matrix[0].size(); 
  if (!rows || !cols) return false; 
  vector<int> column_data; 
  for (int idx = 0; idx < rows; ++idx) column_data.emplace_back(matrix[idx][0]); 
  const auto [col_idx, status_1] = BinarySearch(column_data, target); 
  if (status_1) return true; 
  if (col_idx == -1) return false; 
  const auto [row_idx, status_2] = BinarySearch(matrix[col_idx], target); 
  return status_2; 
} 
```

- **Rotated array search** 

```c++
// 33. Search in Rotated Sorted Array 
int search(vector<int>& nums, int target) { 
  if (!nums.size()) return -1; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    const int val = nums[mid_idx]; 
    if (nums[stt_idx] < val) { 
      if (val >= target && nums[stt_idx] <= target) fin_idx = mid_idx; 
      else stt_idx = mid_idx; 
    } else { 
      if (val <= target && nums[fin_idx] >= target) stt_idx = mid_idx; 
      else fin_idx = mid_idx; 
    } 
  } 
  if (nums[stt_idx] == target) return stt_idx; 
  if (nums[fin_idx] == target) return fin_idx; 
  return -1; 
} 

// 81. Search in Rotated Sorted Array II 
bool search(vector<int>& nums, int target) { 
  if (!nums.size()) return false; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    const int val = nums[mid_idx]; 
    if (nums[stt_idx] < val) { 
      if (val >= target && nums[stt_idx] <= target) fin_idx = mid_idx; 
      else stt_idx = mid_idx; 
    } else if (nums[stt_idx] > val){ 
      if (val <= target && nums[fin_idx] >= target) stt_idx = mid_idx; 
      else fin_idx = mid_idx; 
    } else { 
      ++stt_idx; 
    }        
  } 
  if (nums[stt_idx] == target || nums[fin_idx] == target) return true; 
  return false;   
}  
```

-  **Find minimum in rotated array** 

```c++
// 153. Find Minimum in Rotated Sorted Array 
int findMin(vector<int>& nums) { 
  if (!nums.size()) return -1; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    const int val = nums[mid_idx]; 
    const int stt_val = nums[stt_idx]; 
    const int fin_val = nums[fin_idx]; 
    if (val > stt_val) { 
      if (stt_val > fin_val) stt_idx = mid_idx; 
      else fin_idx = mid_idx; 
    } else { 
      fin_idx = mid_idx; 
    } 
  } 
  return nums[stt_idx] < nums[fin_idx] ? nums[stt_idx]:nums[fin_idx]; 
} 

// 154. Find Minimum in Rotated Sorted Array II 
int findMin(vector<int>& nums) { 
  if (!nums.size()) return -1; 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    const int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    const int val = nums[mid_idx]; 
    const int stt_val = nums[stt_idx]; 
    const int fin_val = nums[fin_idx]; 
    if (val >= stt_val) { 
      if (stt_val < fin_val) fin_idx = mid_idx; 
      else if (stt_val > fin_val) stt_idx = mid_idx; 
      else ++stt_idx; 
    } else { 
      if (stt_val > fin_val) fin_idx = mid_idx; 
      else ++stt_idx; 
    } 
  } 
  return nums[stt_idx] < nums[fin_idx] ? nums[stt_idx] : nums[fin_idx]; 
} 

// 88. Merge Sorted Array 
void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) { 
  for (int idx = m - 1; idx >= 0; --idx) nums1[n+idx] = nums1[idx]; 
  int stt_1 = n; 
  int stt_2 = 0; 
  int cur = 0; 
  while (stt_2 < n && stt_1 < m+n) { 
    if (nums1[stt_1] <= nums2[stt_2]) nums1[cur++] = nums1[stt_1++];   
    else nums1[cur++] = nums2[stt_2++]; 
  } 
  if (stt_2 < n) { 
    for (int i = cur; i < n + m; ++i) nums1[i] = nums2[stt_2++]; 
  } 
} 
```

- **3 Steps Reverse** 

```c++
// 796. Rotate String 
// method 1 
bool rotateString(string A, string B) { 
  const string A2 = A + A; 
  return A2.find(B) != -1 && A.size() == B.size(); 
} 

// method 2 
void ReverseString(string& str, int stt_idx, int fin_idx) { 
  while (stt_idx < fin_idx) { 
    swap(str[stt_idx++], str[fin_idx--]); 
  } 
}   

bool rotateString(string A, string B) { 
  if (A.size() != B.size()) return false; 
  for (int i = 0; i < A.size() - 1; ++i) { 
    string tmp = B; 
    ReverseString(tmp, 0, i); 
    ReverseString(tmp, i+1, B.size() - 1); 
    ReverseString(tmp, 0, B.size() - 1); 
    if (tmp == A) return true; 
  } 
  return false; 
} 

// 151. Reverse Words in a String 
void ReverseString(string& str, int stt_idx, int fin_idx) { 
  while (stt_idx < fin_idx) { 
    swap(str[stt_idx++], str[fin_idx--]); 
  } 
} 

string StripString(string str) { 
  int stt_idx = 0; 
  while (stt_idx < str.size() && str[stt_idx] == ' ') ++stt_idx; 
  int fin_idx = str.size() - 1; 
  while (fin_idx >= 0 && str[fin_idx] == ' ') --fin_idx; 
  string s = str.substr(stt_idx, fin_idx - stt_idx + 1); 
  string fin_s; 
  bool has_empty = false; 
  for (const auto c:s) { 
    if (c == ' ') { 
      if (has_empty) continue; 
      has_empty = true; 
      fin_s += c; 
    } else { 
      has_empty = false; 
      fin_s += c; 
    } 
  } 
  return fin_s; 
} 

string reverseWords(string s) { 
  s = StripString(s); 
  int s_len = s.size(); 
  int stt_idx = -1; 
  for (int idx = 0; idx < s_len; ++idx) { 
    if (s[idx] == ' ') { 
      if (stt_idx != -1) 
        ReverseString(s, stt_idx, idx - 1); 
      stt_idx = -1; 
    } else if (stt_idx == -1) stt_idx = idx; 
  } 
  if (stt_idx != -1) 
    ReverseString(s, stt_idx, s_len - 1); 
  ReverseString(s, 0, s_len - 1); 
  return s; 
} 

// 186. Reverse Words in a String II 
void ReverseString(vector<char>& s, int stt_idx, int fin_idx) { 
  while (stt_idx < fin_idx) { 
    swap(s[stt_idx++], s[fin_idx--]); 
  } 
} 

void reverseWords(vector<char>& s) { 
  int s_len = s.size(); 
  int stt_idx = -1; 
  for (int idx = 0; idx < s_len; ++idx) { 
    if (s[idx] == ' ') { 
      if (stt_idx != -1) ReverseString(s, stt_idx, idx - 1); 
      stt_idx = -1; 
    } else { 
      if (stt_idx == -1) stt_idx = idx; 
    } 
  } 
  if (stt_idx != -1) ReverseString(s, stt_idx, s_len - 1); 
  ReverseString(s, 0, s_len - 1); 
} 

// 557. Reverse Words in a String III 
void ReverseString(string& str, int stt_idx, int fin_idx) { 
  while (stt_idx < fin_idx) { 
    swap(str[stt_idx++], str[fin_idx--]); 
  }  
} 

string reverseWords(string s) { 
  int s_len = s.size(); 
  int stt_idx = -1; 
  for (int idx = 0; idx < s_len; ++idx) { 
    if (s[idx] == ' ') { 
      if (stt_idx != -1) ReverseString(s, stt_idx, idx - 1); 
      stt_idx = -1; 
    } else if (stt_idx == -1){ 
      stt_idx = idx; 
    } 
  } 
  if (stt_idx != -1) 
    ReverseString(s, stt_idx, s_len - 1); 
  return s; 
} 

// 26. Remove Duplicates from Sorted Array 
int removeDuplicates(vector<int>& nums) { 
  int cur_idx = 0; 
  int pre_idx = 0; 
  int nums_len = nums.size(); 
  while (cur_idx < nums_len) { 
    while (cur_idx + 1 < nums_len) { 
      if (nums[cur_idx] == nums[cur_idx + 1]) { 
        ++cur_idx; 
      } else break; 
    } 
    nums[pre_idx] = nums[cur_idx]; 
    ++pre_idx; 
    ++cur_idx; 
  } 
  return !nums_len?0:pre_idx; 
} 
```

- **Find a peak**

```c++
// 162. Find Peak Element 
int findPeakElement(vector<int>& nums) { 
  int stt_idx = 0; 
  int fin_idx = nums.size() - 1; 
  while (stt_idx + 1 < fin_idx) { 
    int mid_idx = stt_idx + (fin_idx - stt_idx) / 2; 
    if (nums[mid_idx] < nums[mid_idx+1]) stt_idx = mid_idx; 
    else if (nums[mid_idx] < nums[mid_idx-1]) fin_idx = mid_idx; 
    else return mid_idx; 
  } 
  return nums[fin_idx] > nums[stt_idx]?fin_idx:stt_idx; 
} 
```

-  **Median of two sorted arrays** 

```c++
// 4. Median of Two Sorted Arrays 
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) { 
  int len1 = nums1.size(); 
  int len2 = nums2.size(); 
  int len = len1 + len2; 
  if (!len) return 0; 
 return len&1 ?  
        MedianSortedArray(nums1, nums2, len/2 + 1): 
        (MedianSortedArray(nums1, nums2, len/2 + 1) +  
         MedianSortedArray(nums1, nums2, len/2)) * 0.5; 
} 

double MedianSortedArray(vector<int>& nums1, vector<int>& nums2, int k) { 
  int stt_1 = 0; 
  int stt_2 = 0; 
  while (k > 1) { 
    int idx_1 = stt_1 + k / 2 - 1; 
    int val_1 = idx_1 >= nums1.size()?INT_MAX:nums1[idx_1]; 
    int idx_2 = stt_2 + k / 2 - 1; 
    int val_2 = idx_2 >= nums2.size()?INT_MAX:nums2[idx_2]; 
    if (val_1 <= val_2) stt_1 += k/2; 
    else stt_2 += k/2; 
    k -= k/2; 
  } 
  if (stt_1 >= nums1.size()) return nums2[stt_2]; 
  if (stt_2 >= nums2.size()) return nums1[stt_1]; 
  return nums1[stt_1] <= nums2[stt_2]?nums1[stt_1]:nums2[stt_2]; 
}  
```

