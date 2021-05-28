---
layout: post
title: DP 1 single sequence
date: 2021-06-26
categories: [technology]
tags: [coding]
comments: false
---

- **Sequence DP**

```c++
// 1872. Stone Game VIII
int stoneGameVIII(vector<int>& w) {
  vector<int> s(w.size()+1, 0);
  vector<int> f(w.size()+1, 0);
  reverse(w.begin(), w.end());
  for (int i = 1; i <= w.size(); ++i) {
    s[i] = s[i-1] + w[i-1]; 
  }
  int v = s[w.size()];
  for (int i = 2; i <= w.size(); ++i) {
    f[i] = v;
    v = max(v, s[w.size()] - s[i-1] - f[i]);
  }
  return f[w.size()];
}

// 1871. Jump Game VII
bool canReach(string str, int minJump, int maxJump) {
  vector<int> f(str.size()+1, 0);
  vector<int> s(str.size()+1, 0);
  s[1] = f[1] = 1;
  for (int i = 2; i <= str.size(); ++i) {
    if (str[i-1] == '0' && i - minJump >= 1) {
      int l = max(1, i - maxJump);
      int r = i - minJump;
      if (s[r] > s[l-1]) f[i] = 1;
    }
    s[i] = s[i-1] + f[i]; 
  }
  return f[str.size()];
}

// 53. Maximum Subarray
// Dynamic programming
// f(i)=max{f(i−1)+nums[i],nums[i]}
int maxSubArray(vector<int>& nums) {
  int cur_sum = nums[0];
  int max_sum = nums[0];
  for (int i = 1; i < nums.size(); ++i) {
    cur_sum = max(cur_sum + nums[i], nums[i]);
    max_sum = max(max_sum, cur_sum);
  }
  return max_sum;
}
// Kadane's algorithm
// dp[i] = Math.max(dp[i - 1], 0) + nums[i]

//42. Trapping Rain Water
int trap(vector<int>& height) {
  if (height.empty()) return 0;
  
  vector<int> left_height(height.size(), 0);
  vector<int> right_height(height.size(), 0);
  int max_left = height[0];
  for (int i = 0; i < height.size(); ++i) {
    max_left = max(height[i], max_left);
    left_height[i] = max_left;
  }
  int max_right = height[height.size()-1];
  for (int i = height.size()-1; i >= 0; --i) {
    max_right = max(height[i], max_right);
    right_height[i] = max_right;
  }
  int count = 0;
  for (int i = 0; i < height.size(); ++i) {
    count += min(left_height[i] - height[i], right_height[i] - height[i]);
  }
  return count;
}

// 84. Largest Rectangle in Histogram
int largestRectangleArea(vector<int>& heights) {
  if (heights.empty()) return 0;
  
  vector<int> left_index(heights.size(), 0);
  vector<int> right_index(heights.size(), 0);
  left_index[0] = -1;
  right_index[heights.size()-1] = heights.size();
  
  for (int i = 1; i < heights.size(); ++i) {
    int t = i - 1;
    while (t >= 0 && heights[t] >= heights[i]) t = left_index[t];
    left_index[i] = t;
  }
  
  for (int i = heights.size() - 1; i >= 0; --i) {
    int t = i + 1;
    while (t < heights.size() && heights[t] >= heights[i])
      t = right_index[t];
    right_index[i] = t;
  }
  
  int max_area = 0;
  for (int i = 0; i < heights.size(); ++i) {
    max_area = max(max_area, heights[i] * (right_index[i] - left_index[i] - 1));
  }
  
  return max_area;
}

// 1770. Maximum Score from Performing Multiplication Operations
bool init = false;
int record[1001][1001];  
int maximumScore(vector<int>& nums, vector<int>& multi, 
                 const int left = 0, const int count = 0) {
  if (count == multi.size()) return 0;
  if (!init) {
    init = true;
    memset(record, 128, sizeof(record));
  }
  if (record[left][count] >= -1.0e9) return record[left][count];
  const int left_val = maximumScore(nums, multi, left, count + 1) +
           multi[count]*nums[nums.size()-count+left-1];
  const int right_val = maximumScore(nums, multi, left+1, count + 1) +
           multi[count]*nums[left];
  return record[left][count] = max(left_val, right_val);
}

// 70. Climbing Stairs
int climbStairs(int n) {
  int n_2 = 1;
  int n_1 = 1;
  for (int i = 1; i < n; ++i) {
    int tmp = n_1;
    n_1 += n_2;
    n_2 = tmp;
  }
  return n_1;
}

// 746. Min Cost Climbing Stairs
int minCostClimbingStairs(vector<int>& cost) {
  int c_1 = 0;
  int c_2 = 0;
  for (int i = 2; i <= cost.size(); ++i) {
    int tmp = c_2;
    c_2 = min(c_1 + cost[i-2], c_2 + cost[i-1]);
    c_1 = tmp;
  }
  return c_2;
}

// 55. Jump Game
bool canJump(vector<int>& nums) {
  int step = nums[0];
  for (int idx = 1; idx < nums.size(); ++idx) {
    --step;
    if (step < 0) return false;
    if (nums[idx] > step) step = nums[idx];
  }
  return true;
}

// 45. Jump Game II
// BFS, not DP really.
int jump(vector<int>& nums) {
  int stt_idx = 0;
  int fin_idx = 0;
  int step = 0;
  while (fin_idx < nums.size() - 1) {
    int max_idx = fin_idx;
    ++step;
    for (int idx = stt_idx; idx <= fin_idx; ++idx)
      max_idx = max(max_idx, idx + nums[idx]);   
    stt_idx = fin_idx + 1;
    fin_idx = max_idx;
  }
  return step;
}

// 300. Longest Increasing Subsequence
int lengthOfLIS(vector<int>& nums) {
  vector<int> record(nums.size() + 1, 1);
  record[nums.size()] = 0;
  for (int i = 0; i < nums.size(); ++i) {
    for (int j = 0; j < i; ++j)
      if (nums[i] > nums[j]) record[i] = max(record[i], record[j] + 1);
  }
  return *max_element(record.begin(), record.end());
}

// 5. Longest Palindromic Substring
// LCS and expanding center.
// Init 1, 2 characters & bottom to top & return maximum length of string
string longestPalindrome(string s) {
  if (s.size() == 0) return "";
  int s_len = s.size();
  vector<vector<bool>> record(s_len, vector<bool>(s_len, false));
  for (int i = 0; i < s_len; ++i) {
    record[i][i] = true;
    if (i > 0 && s[i-1] == s[i]) record[i-1][i] = true;
  }
  for (int i = 0; i < s_len; ++i) {
    for (int j = 0; j < i - 1; ++j) {
      if (s[i] == s[j]) {
        if (i - j >= 2 && record[j + 1][i - 1]) 
          record[j][i] = true;
      }
    }
  }
  for (int i = s_len - 1; i >= 0; --i) {
    for (int j = 0; j < s_len - i; ++j) {
      if (record[j][i + j]) return s.substr(j, i + 1);
    }
  }
  return "";
}

// 132. Palindrome Partitioning II
void GetPalindrome(vector<vector<bool>>& is_palindrome, string s) {
  for (int i = 0; i < s.size(); ++i) is_palindrome[i][i] = true;
  for (int i = 0; i < s.size() - 1; ++i) is_palindrome[i][i+1] = s[i] == s[i+1];
  for (int diff = 2; diff < s.size(); ++diff) {
    for (int stt = 0; stt + diff <s.size(); ++stt) {
      is_palindrome[stt][stt+diff] = is_palindrome[stt+1][stt+diff-1] 
                                  && (s[stt] == s[stt+diff]);
    }
  }
}  
  
int minCut(string s) {
  if (!s.size()) return 0;
  vector<int> record(s.size() + 1);
  for (int i = 0; i < record.size(); ++i) 
    record[i] = i - 1;
  vector<vector<bool>> is_palindrome(s.size(), vector<bool>(s.size(), false));
  GetPalindrome(is_palindrome, s);
  for (int len = 1; len <= s.size(); ++len) {
    for (int i = 1; i <= len; ++i) {
      if (is_palindrome[i-1][len-1])
        record[len] = min(record[len], record[i - 1] + 1);
    }
  }    
  return record[s.size()];
}

// 647. Palindromic Substrings
// The palindromic is incremental
int countSubstrings(string s) {
  int s_len = s.size();
  if (s_len == 0) return 0;
  vector<vector<bool>> record(s_len, vector<bool>(s_len, 0));
  int cnt = s_len;
  record[0][0] = true;
  for (int i = 1; i < s_len; ++i) {
    record[i][i] = true;
    if (s[i-1] == s[i]) {
      record[i-1][i] = true;
      ++cnt;
    }
  }
  for (int i = 0; i < s_len; ++i) {
    for (int j = 0; j < i - 1; ++j) {
      if (s[i] == s[j] && record[j+1][i-1]) {
        ++cnt;
        record[j][i] = true;
      }
    }
  }
  return cnt;
}

// 516. Longest Palindromic Subsequence
int longestPalindromeSubseq(string s) {
  int s_len = s.size();
  if (s_len == 0) return 0;
  vector<vector<int>> record(s_len, vector<int>(s_len, 1));
  for (int i = 1; i < s_len; ++i) 
    if (s[i-1] == s[i])
      record[i-1][i] = 2;
      
  for (int i = 0; i < s_len; ++i) {
    for (int j = i - 2; j >= 0; --j) {
      record[j][i] = max(record[j+1][i-1] + (s[i] == s[j]?2:0),
                         max(record[j+1][i], record[j][i-1]));
    }
  }    
  return record[0][s_len-1];
}

// 139. Word Break
void GetInitialStatus(string s, vector<vector<bool>>& record, vector<string>& wordDict) {
  for (const auto word : wordDict) {
    int pos = 0;
    while ( (pos = s.find(word, pos)) != string::npos) {
      record[pos][pos + word.size() - 1] = true;
      ++pos;
    }
  }
}  
  
bool wordBreak(string s, vector<string>& wordDict) {
  vector<vector<bool>> record(s.size(), vector<bool>(s.size(), false));
  GetInitialStatus(s, record, wordDict);
  for (int len = 0; len < s.size(); ++len) {
    for (int stt = 0; stt < len; ++stt) {
      if (record[0][stt] && record[stt + 1][len]) record[0][len] = true;
    }
  }
  return record[0][s.size() - 1];
}

// 32. Longest Valid Parentheses
// string & sequence difference
int longestValidParentheses(string s) {
  int s_len = s.size();
  if (s_len == 0) return 0;
  vector<int> record(s_len, 0);  
  int max_val = 0;
  int left_brace = 0;
  for (int i = 0; i < s_len; ++i) {
    if (s[i] == '(') ++left_brace;
    else if (left_brace > 0) {
      record[i] = record[i - 1] + 2;
      --left_brace;
      if (i - record[i] > 0) record[i] += record[i - record[i]];
      max_val = max(max_val, record[i]);
    }
  }
  return max_val;
}
// Stack solution.
int longestValidParentheses(string s) {
  stack<int> record;
  int count = 0;
  for (const auto& c : s) {
    if (c == '(') record.push(count);
    else {
      if (record.empty()) record.push(count);
      else {
        if (s[record.top()] == '(')
          record.pop();
        else 
          record.push(count);
      }
    }
    ++count;
  }
  if (record.empty()) return s.size();
  int a = s.size();
  int max_len = 0;
  while (!record.empty()) {
    const int tp = record.top();
    record.pop();
    max_len = max(max_len, a - tp - 1);
    a = tp;
  }
  max_len = max(max_len, a);
  
  return max_len;
}

// 1218. Longest Arithmetic Subsequence of Given Difference
int longestSubsequence(vector<int>& arr, int diff) {
  unordered_map<int, int> record;
  for (const auto& num : arr) {
    if (record.find(num - diff) != record.end()) {
      record[num] = max(record[num], record[num - diff] + 1);
    } else {
      record[num] = max(record[num], 1);
    }
  }
  int res = 0;
  for_each(begin(record), end(record), [&](const pair<int, int>& num) {
    res = max(num.second, res);
  });
  return res;
}
```

