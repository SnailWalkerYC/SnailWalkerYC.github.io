---
layout: post
title: ordered map and brainteaser and minimax
date: 2020-06-08
categories: [technology]
tags: [tech]
comments: false
---



### Ordered Map(ordered set)

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


```





### Brainteaser





### Minimax



