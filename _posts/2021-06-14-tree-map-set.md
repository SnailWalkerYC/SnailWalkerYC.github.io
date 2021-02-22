---
layout: post
title: tree map set
date: 2021-06-14
categories: [technology]
tags: [tech]
comments: false
---

### Ordered Map(ordered set)

- Ordered Map: **at**(can be used in const map; and throw exception)，比[]好在如果不存在就会抛出异常，但是[]不会抛出，不存在就插入; begin, cbegin, end, cend, crbegin, crend -> bidirectional; **clear**: remove all elements and set size to 0. Could use the "**count**" to calculate. **Emplace_hint:** return the position of the same member. **equal_range:** pair of [lower_bound, upper_bound]. **Find:** find the iterator, otherwise the end will be returned.   Insert: insert iterator的时候，结束的是不包括在里面的; key_comp, value_comp, lower_bound, upper_bound: 返回对比的结果。
- 对于interval的常见套路：固定一个size k，进行添加和删除；使用iterator进行操作，可以实现log(n)的效果。另外有个套路用来记录interval的：https://leetcode.com/problems/meeting-rooms-ii/discuss/203658/HashMapTreeMap-resolves-Scheduling-Problem

```c++
// 220. Contains Duplicate III
class Solution {
 public:
  bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
    if (!k) return false;
    map<long, int> record;
    for (int i = 0; i < nums.size(); ++i) {
      const auto low = record.lower_bound(static_cast<long>(nums[i])-t);
      const auto upper = record.upper_bound(static_cast<long>(nums[i])+t);
      ++record[nums[i]];
      if (low != upper) return true;
      if (i >= k) {
        --record[nums[i-k]];
        if (!record[nums[i-k]])
          record.erase(nums[i-k]);
      }
    }
    return false;
  }
};

// 352. Data Stream as Disjoint Intervals
class SummaryRanges {
  map<int, int> record_;
  
 public:
  /** Initialize your data structure here. */
  SummaryRanges() {
        
  }
    
  void addNum(int val) {
    // Merge previous
    if (record_.find(val) == record_.end()) {
      auto it = record_.upper_bound(val);
      if (it != record_.end() && it != record_.begin()) {
        --it;
        if (it->first + it->second == val) {
          ++it->second;
        } else if (it->first + it->second <= val) {
          ++record_[val];
        } 
      } else if (it == record_.end() && record_.size() > 0) {
        auto it2 = record_.rbegin();
        if (it2->first + it2->second == val) {
          ++it2->second;
        } else if (it2->first + it2->second <= val) {
          ++record_[val];
        } 
      } 
      else {
        ++record_[val];
      }
      
      // Merge later part
      if (record_.find(val+1) != record_.end()) {
        auto it = record_.find(val+1);
        --it;
        it->second += record_[val+1];
        record_.erase(val+1);
      }
    }  
  }
    
  vector<vector<int>> getIntervals() {
    vector<vector<int>> ans;
    for (const auto [num, cnt] : record_) {
      ans.push_back({num, num + cnt - 1});
    }
    return ans;
  }
};

/**
 * Your SummaryRanges object will be instantiated and called as such:
 * SummaryRanges* obj = new SummaryRanges();
 * obj->addNum(val);
 * vector<vector<int>> param_2 = obj->getIntervals();
 */

// 1604. Alert Using Same Key-Card Three or More Times in a One Hour Period
class Solution {
  unordered_map<string, vector<string>> record_;
  
  pair<int, int> GetTime(const string& a) {
    return {10*(a[0]-'0') + a[1] - '0',
            10*(a[3]-'0') + a[4] - '0'};
  }
  
  bool InOneHour(const string a, const string b) {
    if (a > b) return false;
    const auto time_a = GetTime(a);
    const auto time_b = GetTime(b);
    return time_b.first == time_a.first ||
      (time_b.first - time_a.first == 1 && time_b.second <= time_a.second);
  }
  
 public:
  vector<string> alertNames(vector<string>& keyName, vector<string>& keyTime) {
    vector<string> alert_list;
    
    for (int i = 0; i < keyName.size(); ++i) {
      record_[keyName[i]].push_back(keyTime[i]);
    }
    
    for (auto& [name, record] : record_) {
      sort(begin(record), end(record));
    }
    
    for (const auto& [name, record] : record_) {
      for (int i = 2; i < record.size(); ++i) {
        if (InOneHour(record[i-2], record[i])) {
          alert_list.push_back(name);
          break;
        }
      }
    }
    
    sort(begin(alert_list), end(alert_list));
    return alert_list;
  }
};

// 846. Hand of Straights
class Solution {
 public:
  bool isNStraightHand(vector<int>& hand, int W) {
    int len_group = hand.size()/W;
    int rem = hand.size()%W;
    if (rem)
      return false;
    map<int, int> rec;
    for (auto ele:hand)
      rec[ele]++;
    while (!rec.empty()) {
      const int start = rec.begin()->first;
      const int num = rec.begin()->second;
      for (int i = 0; i < W; ++i) {
        rec[start+i] -= num;
        if (!rec[start+i]) rec.erase(start+i);
        else if(rec[start+i] < 0) return false;
      }
    }
    return true;
  }
};

// 729. My Calendar I
// 731. My Calendar II
class MyCalendarTwo {
 private:
  map<int, int> record_;
  int threshold_ = 2;
  
 public:
  MyCalendarTwo() {}
    
  bool book(int start, int end) {
    ++record_[start];    
    --record_[end];
    int count = 0;
    for (const auto& [ts, num] : record_) {
      count += num;
      if (count > threshold_) {
        --record_[start];
        ++record_[end];
        return false;
      }
    }
    return true;
  }
};

// 715. Range Module
class RangeModule {
  map<int, int> record_;
 public:
  RangeModule() {
        
  }
    
  void addRange(int left, int right) {
    auto lower_it = record_.lower_bound(left);
    auto upper_it = record_.upper_bound(right);
    if (lower_it != record_.begin()) {
      --lower_it;
      if (lower_it->second < left) ++lower_it;
    }
    if (lower_it != upper_it) {
      left = min(left, lower_it->first);
      right = max(right, (--upper_it)->second);
      record_.erase(lower_it, ++upper_it);
    }
    record_[left] = right;
  }
    
  bool queryRange(int left, int right) {
    auto it = record_.upper_bound(left);
    if (it == record_.begin() || (--it)->second < right)
      return false;
    return true;
  }
    
  void removeRange(int left, int right) {
    auto it1 = record_.upper_bound(left);
    auto it2 = record_.upper_bound(right);
    if (it1 != record_.begin()) {
      --it1;
      if (it1->second < left) {
        ++it1;
      }
    }
    if (it1 == it2) return;
    int l1 = min(left, it1->first);
    int r2 = max(right, (--it2)->second);
    record_.erase(it1, ++it2);
    if (l1 != left) record_[l1] = left;
    if (r2 != right) record_[right] = r2;
  }
};

// 732. My Calendar III
class MyCalendarThree {
public:
  map<int, int> record_;
  
  MyCalendarThree() {}
    
  int book(int start, int end) {
    ++record_[start];
    --record_[end];
    int count = 0;
    int max_count = 0;
    for (auto it = record_.begin(); it != record_.end(); ++it) {
      count += it->second;
      max_count = max(count, max_count);
    }
    return max_count;
  }
};

// 252. Meeting Rooms
class Solution {
 public:
  static bool compareAll(const vector<int>& v1, const vector<int>& v2) {
    return v1[0] < v2[0] || (v1[0]==v2[0] && v1[1] < v2[1]);
  }
  bool canAttendMeetings(vector<vector<int>>& intervals) {
    if (!intervals.size()) {
      return true;
    }
    sort(intervals.begin(), intervals.end(), compareAll);    
    int cur_max = INT_MIN;    
    for (auto ele: intervals) {
      if (ele[0] < cur_max) {
        return false;
      }
      cur_max = ele[1];
    }
    return true;
  }
  
  bool canAttendMeetings2(vector<vector<int>>& intervals) {
    map<int, int> intervals_mp;
    for (const auto& interval : intervals) {
      ++intervals_mp[interval[0]];
      --intervals_mp[interval[1]];
    }
    int count = 0;
    for (const auto& interval : intervals_mp) {
      count += interval.second;
      if (count > 1) return false;
    }
    return true;
  }
};

// 253. Meeting Rooms II
int minMeetingRooms(vector<vector<int>>& intervals) {
  map<int, int> record;
  for (const auto& interval : intervals) {
    ++record[interval[0]];
    --record[interval[1]];
  }
  int count = 0;
  int max_count = 0;
  for (const auto& interval : record) {
    count += interval.second;
    max_count = max(max_count, count);
  }
  return max_count;
}

// 56. Merge Intervals
vector<vector<int>> merge(vector<vector<int>>& intervals) {
  map<int, int> rec_int;
  for (auto inter: intervals) {
    if (rec_int.find(inter[0]) == rec_int.end() ||
      rec_int[inter[0]] < inter[1]) {
      rec_int[inter[0]] = inter[1];
    }
  }
  vector<vector<int>> res_int;
  for (auto ele:rec_int) {
    if (!res_int.size() || res_int.back()[1] < ele.first) {
      res_int.push_back(vector<int>({ele.first,ele.second}));
    }
    else {
      int left = res_int.back()[0];
      int right = max(res_int.back()[1], ele.second);
      res_int.pop_back();
      res_int.push_back(vector<int>({left, right}));
    }
  }
  return res_int;
}

// 57. Insert Interval
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

// 855. Exam Room


// To do problems:
// 1606, 975, 699, 683.
```



