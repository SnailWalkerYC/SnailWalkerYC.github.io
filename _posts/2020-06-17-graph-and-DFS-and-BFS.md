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


To 854.
```

