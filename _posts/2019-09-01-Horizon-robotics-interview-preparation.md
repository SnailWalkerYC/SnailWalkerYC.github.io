---
layout: post
title: Horizon robotics interview preparation
date: 2019-09-01
categories: [technology]
tags: [coding]
comments: false
---



### Computation Complexity

https://liam.page/2016/06/20/big-O-cheat-sheet/



#### 269. Alien Dictionary

```c++
bool SearchEdges(const vector<string>& words, const int index,
                 unordered_map<char, unordered_set<char>>& edges) {
  if (!words.size()) return true;
    
  char last = words[0][index];
  vector<string> history;
  for (int i = 0; i < words.size(); ++i) {
    if (words[i][index] != last) {
      edges[last].insert(words[i][index]);
      last = words[i][index];
      bool status = SearchEdges(history, index + 1, edges);
      if (!status) return false;
      history.clear();
    } else {
      if (edges.find(words[i][index]) == edges.end())
        edges[words[i][index]] = {};
    }
    if (words[i].size() > index + 1) 
      history.push_back(words[i]);
    else {
      if (history.size() > 0) return false;
    }
  }
  bool status = true;
  if (history.size() > 0) {
    status = SearchEdges(history, index + 1, edges);
  }
  return status;
}
  
string alienOrder(vector<string>& words) {
  unordered_map<char, unordered_set<char>> edges;
  bool status = SearchEdges(words, 0, edges);
  if (!status) return "";
  vector<int> indegrees(26, 0);
  vector<bool> ingraph(26, 0);
  for (const auto& [node, edge] : edges) {
    ingraph[node-'a'] = true;
    for (const auto& v : edge) {
      ++indegrees[v-'a'];
      ingraph[v-'a'] = true;
    }
  }
  queue<int> cur;
  for (int i = 0; i < 26; ++i) {
    if (ingraph[i] && !indegrees[i]) {
      cur.push(i);
    }
  }
  string ans;
  while (!cur.empty()) {
    const auto tp = cur.front();
    ans += char(tp + 'a');
    cur.pop();
    for (const auto& edge : edges[tp+'a']) {
      --indegrees[edge-'a'];
      if (!indegrees[edge-'a']) {
        cur.push(edge-'a');
      }
    }
  }
  for (int i = 0; i < 26; ++i) {
    if (indegrees[i]) return "";
  }
  return ans;
}
```





#### 953. Verify an Alien Dictionary

```c++
bool isAlienSorted(vector<string>& words, string order) {
  unordered_map<char, int> order_map;
  for (int i = 0; i < order.size(); ++i)
    order_map[order[i]] = i;
  for (int i = 1; i < words.size(); ++i) {
    if (Bigger(words[i-1], words[i], order_map))
      return false;
  }
  return true;
}
  

bool Bigger(const string& s1, const string& s2, 
            unordered_map<char, int>& order_map) {
  for (int i = 0; i < s1.size(); ++i) {
    if (s2.size() <= i) return true;
    if (order_map[s1[i]] > order_map[s2[i]])
      return true;
    else if (order_map[s1[i]] < order_map[s2[i]])
      return false;
  }
  return false;
}  
```

