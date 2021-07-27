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





# Onsite

```c++
// 1. Permutation II  2. Next Permutation
// 272
// System Design: 设计LinkedIn Home Page包括comments.
// sysem design： monitor system System Design. 设计一个activity/health monitoring system for data center
// Design Blacklist service， 提供一个isMalicious(ip)的API，设计系统防止恶意攻击。主要难点在于sync各个server的blacklist，initialize new added server... functional/non-functional requirements 假设一天1Billion访问量，5%是malicious ip造成的，估算下QPS和storage，可以跟面试官negotiate.  High level  LB layer, app server layer 然后request manager，ip要是没见过直接调用api，然后存进数据库。后面优化app servers本地存blacklist, 用kafka sync，backup可以让request manager去初始化
// isIsomorphic string, finding isomorphic groups
蠡口516
2. 蠡口277
3. 蠡口207
432
// top k in 5min , store document, query words, 给你一个is malicious ip的api,  design a service to catch malicious ip
// 636
// 160, with cycle; 272, log n
// 716,   
// 72  
// 68
// 33
// LCA, InsertRemoveRemoveRandom O(1), follow up Duplicates  
// 380
// 34, 236
//   

coding module1: 尔唔遛 尔溜吾
coding module2: 尔酒企 尔领悟 感谢国人小姐姐放水

2. Calendar system, like outlook.
4. design rate limiter
5. 给一个 isMaliciousIP api, design a system to catch malicious IP
6. 设计领英主页面，包括comments
7. 设计一个health monitoring system
8. 设计一个聊天室
9. 设计shorten url， tiny url 常见问题 简单讨论eventually consistency， cache， regional data center
10. 设计一个图书馆
第五轮：infra design（complex system）
设计一个用户行为收集和查询系统，可以收集各种event，比如click/view/like...
可以根据时间、行为类型等等进行查询
给出high level设计后基本全在讨论kafka的各种细节，领英真的很喜欢问kafka，面他家infra最好看看kafka的论文和最近的update
infra design (algo & ds)
建立倒排索引，本来以为要写代码，结果全在讨论数据如何存储如何scale
  
蠡寇要散酒，姚耀叁  
68, 366,   
650, 8  
449, deserialize
tiny url  
code: max tree + followup 用treemap的解法 没写code + 第二问是一道比较open的code讨论题 没有具体答案
76  
  
System Design:
1. LinkedIn System https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=556281&highlight=%C1%EC%D3%A2
2. 
```



## System Design

```
# Top k: heavy hitters, top k for 5 minutes, 1 hour, 1 day. Design log system. You have a lot of servers and each is producing thousands of logs per second. You have unlimited resources. Design a system that will aggregate all the logs from all the systems, for a given window of 24 hours. And need to return the top N exceptions.
```



![cherimola](../images/topk1.png)

![cherimola](../images/topk2.png)

![cherimola](../images/topk3.png)



![cherimola](../images/topk4.png)



![cherimola](../images/topk5.png)



![cherimola](../images/topk6.png)



![cherimola](../images/topk7.png)



![cherimola](../images/topk8.png)



![cherimola](../images/topk9.png)



![cherimola](../images/topk10.png)



![cherimola](../images/topk11.png)



![cherimola](../images/topk12.png)



![cherimola](../images/topk13.png)



![cherimola](../images/topk14.png)

```
# Rate limiting: 
```





```
# 设计一个KV store，包含文件存储格式.
```

