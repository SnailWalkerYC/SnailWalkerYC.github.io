---
layout: post
title: graph shortest path
date: 2021-07-04
categories: [technology]
tags: [tech]
comments: false
---

### 最短路径



```c++
// 1786. Number of Restricted Paths From First to Last Node
struct Node {
  int num_ = INT_MIN;
  int dis_ = INT_MAX;
  int index_ = 0;
  Node(int idx, int dis) {
    index_ = idx;
    dis_ = dis;
  }
};  
 
struct Comp {
  bool operator()(const Node& node1, const Node& node2) {
    return node1.dis_ > node2.dis_;
  }
};

int DFS(const int num, const unordered_map<int, unordered_set<int>>& edges, vector<Node>& record) {
  if (record[num].num_ != INT_MIN) return record[num].num_;
  if (num == record.size()-1) {
    record[num].num_ = 1;
    return 1;
  }
  if (edges.find(num) == edges.end()) return 0;
  
  const int cur_dis = record[num].dis_;
  long long count = 0;
 
  for (const auto node : edges.at(num)) {
    if (record[node].dis_ < cur_dis) {
      count += DFS(node, edges, record);
      count %= (long)(1e9 + 7);
    } 
  }
  record[num].num_ = count;
  
  return record[num].num_;
}  
  
int countRestrictedPaths(int n, vector<vector<int>>& edges) {
  priority_queue<Node, vector<Node>, Comp> pq;
  vector<Node> record;
  for (int i = 0; i < n; ++i) {
    record.emplace_back(i, INT_MAX);
  }
  pq.push(Node(n, 0));
  record.emplace_back(n, 0);
  unordered_map<int, unordered_set<int>> graph;
  unordered_map<int, int> distance;
  record[n].dis_ = 0;
  for (const auto& edge : edges) {
    graph[edge[0]].insert(edge[1]);
    graph[edge[1]].insert(edge[0]);
    distance[edge[0]*1e5 + edge[1]] = edge[2];
    distance[edge[1]*1e5 + edge[0]] = edge[2];
  }
  while (!pq.empty()) {
    const auto tp = pq.top();
    pq.pop();
    if (tp.dis_ == INT_MAX) break;
    if (graph.find(tp.index_) != graph.end()) {
      for (const auto node : graph[tp.index_]) {
        const int key = 1e5 * tp.index_ + node;
        int new_dis = distance[key] + record[tp.index_].dis_;
        if (record[node].dis_ > new_dis) {
          record[node].dis_ = new_dis;
          pq.push(record[node]);
        }
      }
    }
  }
  return DFS(1, graph, record);
}
```

