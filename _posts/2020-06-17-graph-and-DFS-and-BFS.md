---
layout: post
title: graph and DFS and BFS
date: 2020-06-17
categories: [technology]
tags: [tech]
comments: false
---



### Graph

```c++
// 133. Clone Graph
// BFS
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/
class Solution {
 public:
  unordered_map<Node*, Node*> record_;
  
  Node* cloneGraph(Node* node) {
    if (!node) return nullptr;
    
    queue<Node*> visiting({node});
    
    while (!visiting.empty()) {
      const auto tp = visiting.front();
      visiting.pop();
      if (record_.find(tp) == record_.end()) {
        Node* tp_map = new Node(tp->val);
        record_[tp] = tp_map;
      }
      for (const auto& node : tp->neighbors) {
        if (record_.find(node) == record_.end()) {
          visiting.push(node);
          record_[node] = new Node(node->val);
        } 
        record_[tp]->neighbors.push_back(record_[node]);
      }
    }
    return record_[node];
  }
};
// DFS
class Solution {
 public:
  unordered_map<Node*, Node*> record_;
  
  Node* cloneGraph(Node* node) {
    if (!node) return nullptr;
    
    if (record_.find(node) != record_.end())
      return record_[node];
    
    Node* new_node = new Node(node->val);
    record_[node] = new_node;
    
    for (const auto child_node : node->neighbors) {
      Node* new_child_node = cloneGraph(child_node);
      record_[child_node] = new_child_node;
      new_node->neighbors.push_back(new_child_node);
    }
    
    return record_[node];
  }
};

// 743. Network Delay Time
// Djikstra shortest path
// Old method
class Solution {
 public:
  struct Node {
    int index = -1;  
    int distance = INT_MAX;
    int precurssor = -1;
    Node(const int idx, const int dis) {
      index = idx;
      distance = dis;   
    }   
  };
    
  struct Comp {
    bool operator()(const struct Node& node1, const struct Node& node2) {
      return node1.distance >= node2.distance;  
    }  
  };
    
  using InitQueue = priority_queue<Node, vector<Node>, Comp>;
  InitQueue InitPriorityQueue(const int N, const int K) {
    InitQueue queue;  
    for (int i = 0; i < N; ++i) {
      if (i != K) queue.push(Node(i, INT_MAX));
    }
    Node start(K, 0);
    queue.push(start);
      
    return queue;
  }
    
  int networkDelayTime(vector<vector<int>>& times, int N, int K) {
    InitQueue Q = InitPriorityQueue(N, K);
    unordered_map<int, Node> record;
    record.emplace(K, Node(K, 0)); 
    unordered_map<int, vector<vector<int>>> edges;
    for (const auto time : times) edges[time[0]].push_back(time);
    int longest_dis = -1;
    unordered_set<int> visited; 
    while (!Q.empty()) {
      const auto tp = Q.top();
      if (tp.distance == INT_MAX) { 
        if (record.size() < N)
          return -1; 
        else break;
      }  
      Q.pop();
      if (visited.find(tp.index) != visited.end()) continue; 
      for (const auto edge : edges[tp.index]) {
        const int new_dis = edge[2] + tp.distance;
        if ((record.find(edge[1]) == record.end()) || record.at(edge[1]).distance > new_dis) 
        {
          Node new_node(edge[1], new_dis);
          new_node.precurssor = edge[0];  
          Q.push(new_node);
          record.emplace(edge[1], new_node).first->second = new_node;  
        }  
      }
      visited.insert(tp.index);  
    }
    for (const auto m : record) {
      longest_dis = max(m.second.distance, longest_dis);
    }
    return longest_dis;
  }
};

// 785. Is Graph Bipartite?
// Method 1 UF
class Solution {
 private:
  class UnionFind {
   public:
    UnionFind(const int N) {
      record_.resize(N);
      rank_.resize(N);
      for (int i = 0; i < N; ++i)
        record_[i] = i;
    }
    
    int Find(const int num) {
      if (num != record_[num])
        record_[num] = Find(record_[num]);
      return record_[num];
    }
    
    void Union(const int num1, const int num2) {
      const int p1 = Find(num1);
      const int p2 = Find(num2);
      if (p1 != p2) {
        if (rank_[p1] > rank_[p2]) {
          record_[p2] = p1;
        } else {
          record_[p1] = p2;
          if (rank_[p1] == rank_[p2])
            ++rank_[p2];
        }
      }
    }
    
    vector<int> record_;
    vector<int> rank_;
  };
 public:
  bool isBipartite(vector<vector<int>>& graph) {
    if (graph.size() < 2) return true;
    UnionFind uf(graph.size());
    for (int i = 0; i < graph.size(); ++i) {
      if (!graph[i].size()) continue;
      for (int j = 0; j < graph[i].size(); ++j) {
        for (int k = j + 1; k < graph[i].size(); ++k) {
          uf.Union(graph[i][j], graph[i][k]);
        }       
      }
    }
    for (int i = 0; i < graph.size(); ++i) {
      if (!graph[i].size()) continue;
      for (const auto& v : graph[i]) {
        if (uf.Find(i) == uf.Find(v))
          return false;
      }
    }
    return true;
  }
};
// BFS
bool isBipartite(vector<vector<int>>& graph) {
  unordered_set<int> r1;
  unordered_set<int> r2;
  for (int i = 0; i < graph.size(); ++i) {
    if (!graph[i].size() || r1.count(i) || r2.count(i))
      continue;
    queue<int> visiting({i});
    r1.insert(i);
    bool take_1 = true;
    while (!visiting.empty()) {
      const int size = visiting.size();
      auto& tmp1 = take_1?r1:r2;
      auto& tmp2 = take_1?r2:r1;
      for (int j = 0; j < size; ++j) {
        const int tp = visiting.front();
        visiting.pop();
        for (const auto& v : graph[tp]) {
          if (tmp1.count(v) > 0) return false;
          const auto [it, s] = tmp2.insert(v);
          if (s) visiting.push(v);
        }
      }
      take_1 ^= true;
    }
  }
  return true;
}
// DFS
class Solution {
 public:
  bool DetectCycle(const int num, const vector<vector<int>>& graph,
                   unordered_set<int>& r1, unordered_set<int>& r2) {
    if (r1.count(num)) {
      return false;
    }
    r1.insert(num);
    for (const auto& v:graph[num]) {
      if (r1.count(v)) return true;
      if (DetectCycle(v, graph, r2, r1))
        return true;
    }
    return false;
  }
  
  bool isBipartite(vector<vector<int>>& graph) {
    unordered_set<int> r1;
    unordered_set<int> r2;
    for (int i = 0; i < graph.size(); ++i) {
      if (!graph[i].size() || r1.count(i) || r2.count(i))
        continue;
      if (DetectCycle(i, graph, r1, r2))
        return false;
    }
    return true;
  }
};

// 399. Evaluate Division
// Method 1: DFS
double CalculateEquation(const string& s1, const string& s2, const double cur,
                         unordered_map<string, unordered_map<string, double>>& edges,
                         unordered_set<string>& visited) {
  if (s1 == s2) {
    return edges.count(s1) > 0 ? cur:-1.0;
  }
  if (visited.count(s1) > 0) return -1.0;
  visited.insert(s1);  
  double num = 1.0;
  if (edges.count(s1) > 0 && edges[s1].size() > 0) {
    for (const auto& v : edges[s1]) {
      num = CalculateEquation(v.first, s2, cur * v.second, edges, visited);
      if (num > 0) return num;
    }
  }
  return -1.0;
}

vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, 
                            vector<vector<string>>& queries) {
  unordered_map<string, unordered_map<string, double>> edges;
  for (int i = 0; i < values.size(); ++i) {
    edges[equations[i][0]].emplace(equations[i][1], values[i]);
    edges[equations[i][1]].emplace(equations[i][0], 1.0/values[i]);
  }
  vector<double> ans;
  for (const auto& query : queries) {
    unordered_set<string> visited;
    ans.push_back(CalculateEquation(query[0], query[1], 1.0, edges, visited)); 
  }
  return ans;
}
// Method 2: BFS
vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, 
                            vector<vector<string>>& queries) {
  unordered_map<string, unordered_map<string, double>> edges;
  for (int i = 0; i < values.size(); ++i) {
    edges[equations[i][0]].emplace(equations[i][1], values[i]);
    edges[equations[i][1]].emplace(equations[i][0], 1.0/values[i]);
  }
  vector<double> ans;
  for (const auto& query : queries) {
    unordered_set<string> visited;
    queue<pair<string, double>> que;
    que.emplace(make_pair(query[0], 1.0));
    double num = -1.0;
    while (!que.empty()) {
      const auto tp = que.front();
      que.pop();
      if (visited.count(tp.first) > 0)
        continue;
      if (!edges.count(tp.first)) {
        visited.insert(tp.first);
        continue;
      }
      for (const auto& edge : edges[tp.first]) {
        if (edge.first == query[1]) {
          num = edge.second * tp.second;
          break;
        } else {
          que.emplace(make_pair(edge.first, edge.second * tp.second));
        }
      }
      if (num > 0) break;
      visited.insert(tp.first);
    }
    ans.push_back(num);
  }
  return ans;
}

// Good problem: 631. Design Excel Sum Formula
To 854. 
```

