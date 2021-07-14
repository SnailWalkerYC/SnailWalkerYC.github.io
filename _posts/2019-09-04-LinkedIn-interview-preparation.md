---
layout: post
title: LinkedIn interview preparation
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false
---



```c++
// 47. Permutations II
void Permutate(vector<int>& nums, vector<vector<int>>& ans, 
               const int idx) {
  if (idx == nums.size()) {
    ans.emplace_back(nums);
    return;
  }
  for (int i = idx; i < nums.size(); ++i) {
    if (nums[i] != nums[idx] || i == idx) {
      swap(nums[i], nums[idx]);
      Permutate(nums, ans, idx+1);
      swap(nums[i], nums[idx]);
    }
  }
}  
vector<vector<int>> permuteUnique(vector<int>& nums) {
  vector<vector<int>> ans;
  Permutate(nums, ans, 0);
  return ans;
}

// 53. Maximum Subarray
int maxSubArray(vector<int>& nums) {
  int cur_sum = nums[0];
  int prev_sum = 0;
  int min_sum = 0;
  int res = nums[0];
  for (int i = 1; i < nums.size(); ++i) {
    cur_sum += nums[i];
    prev_sum += nums[i-1];
    min_sum = min(prev_sum, min_sum);
    res = max(cur_sum - min_sum, res);
  }
  res = max(cur_sum, res);
  return res;
}

// 56. Merge Intervals
vector<vector<int>> merge(vector<vector<int>>& intervals) {
  vector<vector<int>> ans;
  sort(begin(intervals), end(intervals), [](
     const vector<int>& v1, const vector<int>& v2) {
    return v1[1] < v2[1];
  });
  for (const auto& val : intervals) {
    if (ans.empty()) {
      ans.emplace_back(val);
    } else {
      while (!ans.empty() && ans.back()[1] >= val[0]) {
        if (ans.back()[0] >= val[0])
          ans.pop_back();
        else break;
      }
      if (ans.empty()) ans.push_back(val);
      else if (ans.back()[1] < val[0]) {
        ans.push_back(val);
      }
      else {
        ans.back()[0] = min(val[0], ans.back()[0]);
        ans.back()[1] = max(val[1], ans.back()[1]);
      }
    }
  }
  return ans;
}

// 57. Insert Interval
// Greedy method
vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {
  auto it=intervals.begin();
  while(it!=intervals.end()&&newInterval.start>it->start){
    ++it;
  }
  intervals.insert(it,newInterval);
  vector<Interval>ans;
  for(const auto interval: intervals){
    if(ans.empty() || ans.back().end<interval.start){
      ans.push_back(interval);
    }
    else {
      ans.back().end=max(ans.back().end,interval.end);
    }    
  }
  return ans;
}
// Binary Seach method

// 41

// 69. Sqrt(x)

// 39

// 40

// 68. Text Justification
string Generate(const int num) {
  if (!num) return "";
  else if (num == 1) return " ";
  const string& space_str = Generate(num/2);
  return space_str + space_str + (num&1?" ": "");
}  
  
vector<string> fullJustify(vector<string>& words, int max_width) {
  vector<string> ans;
  vector<string> cur;
  int cur_len = 0;
  for (const auto& word :words) {
    if (word.size() + cur_len + cur.size() <= max_width) {
      cur.emplace_back(word);
      cur_len += word.size();
    } else {
      string str;
      int rem = cur.size() == 1? 0 : (max_width - cur_len) % (cur.size() - 1);
      int space = cur.size() == 1? max_width - cur_len : (max_width - cur_len) / (cur.size() - 1);
      string space_str = Generate(space);
      string rem_str = Generate(1);
      for (int i = 0, j = 0; i < cur.size() - 1; ++i, ++j) {
        string cur_space = space_str;
        if (j < rem) {
          cur_space += rem_str;
        }
        str += cur[i] + cur_space;
      }
      str += cur.back();
      if (cur.size() == 1)
        str += space_str;
      ans.emplace_back(str);
      cur_len = word.size();
      cur.clear();
      cur.emplace_back(word);
    }
  }
  if (!cur.empty()) {
    string space_str = max_width >= cur_len + cur.size()?Generate(max_width - cur_len - cur.size()):"";
    string str;
    for (const auto& s : cur) {
      str += s + " ";
    }
    str = str.substr(0, max_width);
    str += space_str;
    ans.emplace_back(str);
  }
  return ans;
}

// 69

// 152

// 165

// 423

// 15

// 30

// 104

// 186

// 191

// 339

// 364

// 392

// 415

// 424

// 578

// 612

// 652 

// 926

// 933

// 934

// 935

// 86

// 738

// 859

// 985

// 1361
```

