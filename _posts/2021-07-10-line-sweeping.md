---
layout: post
title: line sweeping
date: 2021-07-10
categories: [technology]
tags: [tech]
comments: false
---

### Line Sweep

```c++
// 1272. Remove Interval
vector<vector<int>> removeInterval(vector<vector<int>>& intervals, vector<int>& toBeRemoved) {
  int idx = 0;
  vector<vector<int>> ans;
  while (idx < intervals.size()) {
    if (toBeRemoved[0] >= intervals[idx][1] ||
        toBeRemoved[1] <= intervals[idx][0]) {
      ans.emplace_back(intervals[idx]);
    } else {
      if (toBeRemoved[0] > intervals[idx][0]) {
        ans.emplace_back(vector<int>({intervals[idx][0], toBeRemoved[0]}));
      } 
      if (toBeRemoved[1] < intervals[idx][1]) {
        ans.emplace_back(vector<int>({toBeRemoved[1], intervals[idx][1]}));
      } 
    } 
    ++idx;
  }
  return ans;
}

// 391. Perfect Rectangle
// Method 1: 用几何学面积和顶点来判断
bool isRectangleCover(vector<vector<int>>& rectangles) {
  map<pair<int, int>, int> pts;
  int area = 0;
  int tp_x = INT_MIN;
  int tp_y = INT_MIN;
  int bm_x = INT_MAX;
  int bm_y = INT_MAX;
  for (const auto& rectangle:rectangles) {
    ++pts[{rectangle[0], rectangle[1]}];
    ++pts[{rectangle[0], rectangle[3]}];
    ++pts[{rectangle[2], rectangle[3]}];
    ++pts[{rectangle[2], rectangle[1]}];
    area += (rectangle[3]-rectangle[1]) * (rectangle[2]-rectangle[0]);
    tp_x = max(rectangle[2], tp_x);
    tp_y = max(rectangle[3], tp_y);
    bm_x = min(rectangle[0], bm_x);
    bm_y = min(rectangle[1], bm_y);
  }
  if (area != (tp_x - bm_x)*(tp_y - bm_y))
    return false;
  int cnt = 0;
  ++pts[{tp_x, tp_y}];
  ++pts[{tp_x, bm_y}];
  ++pts[{bm_x, tp_y}];
  ++pts[{bm_x, bm_y}];
  for (const auto& [_, num] : pts) 
    if (num % 2) return false;
  return true;
}
// Method 2: sweep line
struct Node {
  int x;
  int index;
  bool is_left;
  Node(const int x_init, const int idx, const bool flag) {
    x = x_init;
    index = idx;
    is_left = flag;
  }
};  

bool isRectangleCover(vector<vector<int>>& rectangles) {
  auto CompPq = [&](const Node& n1, const Node& n2) {
    if (n1.x != n2.x) return n1.x > n2.x;
    return rectangles[n1.index][0] > rectangles[n2.index][0]; 
  };
  priority_queue<Node, vector<Node>, decltype(CompPq)> pq(CompPq);
  int bottom = INT_MAX;
  int top = INT_MIN;
  int count = 0;
  for (const auto& rectangle : rectangles) {
    pq.push(Node(rectangle[0], count, true));
    pq.push(Node(rectangle[2], count, false));
    ++count;
    bottom = min(rectangle[1], bottom);
    top = max(rectangle[3], top);
  }
  auto CompNode = [&](const Node& n1, const Node& n2) {
    if (rectangles[n1.index][3] <= rectangles[n2.index][1])
      return true;
    return false;
  };
  set<Node, decltype(CompNode)> record(CompNode);
  const int max_range = top - bottom;
  int cur_range = 0;
  while (!pq.empty()) {
    const int cur_x = pq.top().x;
    while (!pq.empty() && cur_x == pq.top().x) {
      const auto tp = pq.top();
      pq.pop();
      if (tp.is_left) {
        const auto [it, s] = record.insert(tp);  
        if (!s) return false;
        cur_range += rectangles[tp.index][3] - rectangles[tp.index][1];
      } else {
        cur_range -= rectangles[tp.index][3] - rectangles[tp.index][1];
        record.erase(tp);
      }
    } 
    if (!pq.empty() && cur_range != max_range) return false;
  }
  return true;
}
// The cppReference says: In imprecise terms, two objects a and b are considered equivalent if neither compares less than the other: !comp(a, b) && !comp(b, a).

// 218. The skyline problem.
// https://briangordon.github.io/2014/08/the-skyline-problem.html
vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
  vector<pair<int, int>> pts;
  for (const auto& building : buildings) {
    pts.emplace_back(building[0], -building[2]);
    pts.emplace_back(building[1], building[2]);
  }
  sort(begin(pts), end(pts));
  multiset<int> pq({0});
  int prev_max = 0;
  vector<vector<int>> ans;
  for (const auto& pt : pts) {
    if (pt.second < 0) pq.insert(-pt.second);
    else pq.erase(pq.find(pt.second));
    const auto cur_max = *crbegin(pq);
    if (cur_max != prev_max) {
      ans.emplace_back(vector<int>({pt.first, cur_max}));
      prev_max = cur_max;
    }
    
  }
  return ans;
}

// TODO: 850. Rectangle Area II
```

