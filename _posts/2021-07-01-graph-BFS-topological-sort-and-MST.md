---
layout: post
title: graph BFS topological sort and MST
date: 2021-07-01
categories: [technology]
tags: [tech]
comments: false
---





### Topological Sort

```c++
// 1D vector schedule
// 207. Course Schedule
// 210. Course Schedule II
// In-degree 
// 210. Course Schedule II
class Solution {
 public:
  vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    unordered_map<int, vector<int>> record;
    vector<int> indegrees(numCourses, 0);
    for (const auto pre : prerequisites) {
      record[pre[1]].push_back(pre[0]);
      ++indegrees[pre[0]];
    }
    queue<int> status;
    for (int i = 0; i < numCourses; ++i) {
      if (!indegrees[i])
        status.push(i);
    }
    vector<int> top_res;
    while (!status.empty()) {
      const auto it = status.front();
      top_res.push_back(it);
      status.pop();
      for (const auto& ele : record[it]) {
        --indegrees[ele];
        if (!indegrees[ele]) {
          status.push(ele);
        }
      }
    }
    return top_res.size() != numCourses ? vector<int>() :top_res;
  }
};
// DFS
class Solution {
 public:
  bool VisitCourse(const int idx, unordered_map<int, vector<int>>& record,
                   vector<int>& visited, stack<int>& order) {
    if (visited[idx] == 2) return true;
    visited[idx] = 1;
    for (const auto v : record[idx]) {
      if (visited[v] == 1) return false; 
      if (!VisitCourse(v, record, visited, order)) {
        return false;
      }
    }
    
    order.push(idx);
    visited[idx] = 2;
    return true;
  }
  
  vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    vector<int> visited(numCourses, 0);
    unordered_map<int, vector<int>> record;
    for (const auto pre : prerequisites) {
      record[pre[1]].push_back(pre[0]);
    }
    stack<int> order;
    for (int i = 0; i < numCourses; ++i) {
      if (!visited[i]) {
        if (!VisitCourse(i, record, visited, order)) {
          return {};
        }
      }
    }
    
    vector<int> ans;
    while (!order.empty()) {
      ans.push_back(order.top());
      order.pop();
    }
    
    return ans;
  }
};

// 444. Sequence Reconstruction
class Solution {
 public:
  bool sequenceReconstruction(vector<int>& org, vector<vector<int>>& seqs) {
    vector<int> indegrees(org.size()+1, 0);
    vector<bool> has_val(org.size()+1, false);
    unordered_map<int, vector<int>> record;
    for (const auto& seq : seqs) {
      if (seq[0] >= 1 && seq[0] <= org.size())
        has_val[seq[0]] = true;
      else 
        return false;
      int stt = 0;
      for (int i = 1; i < seq.size(); ++i) {
        if (seq[i] < 1 || seq[i] > org.size())
          return false;
        record[seq[stt]].push_back(seq[i]);
        ++indegrees[seq[i]];
        has_val[seq[i]] = has_val[seq[stt]] = true;
        stt = i;
      }
    }
    queue<int> que;
    for (int i = 1; i <= org.size(); ++i) {
      if (!indegrees[i] && has_val[i]) {
        que.push(i);
      }
    }
    int cnt = 0;
    while (!que.empty()) {
      if (que.size() > 1) return false;
      const auto tp = que.front();
      que.pop();
      if (tp != org[cnt++]) return false;
      for (const auto& v : record[tp]) {
        --indegrees[v];
        if (!indegrees[v]) {
          que.push(v);
        }
      }
    }
    
    return cnt == org.size();
  }
};

// 269. Alien Dictionary
class Solution {
 public:
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
        vector<string> empty;
        history.swap(empty);
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
};

// 1203. Sort Items by Groups Respecting Dependencies
class Solution {
 public:
  vector<int> sortItems(int n, int m, vector<int>& group, vector<vector<int>>& beforeItems) {
    
    int gp_id = m;
    for (int i = 0; i < group.size(); ++i) { 
      if (group[i] == -1) {
        group[i] = gp_id++;    
      }
    }
    
    // Items indegrees & edges
    unordered_map<int, unordered_set<int>> item_edges;
    vector<int> item_indegrees(n, 0);
    // Group indegrees & edges
    unordered_map<int, unordered_set<int>> group_edges;
    vector<int> group_indegrees(gp_id, 0);
    
    unordered_map<int, unordered_set<int>> group_item_mp;
    
    for (int i = 0; i < n; ++i) {
      for (const auto& v:beforeItems[i]) {
        item_edges[v].insert(i);
        ++item_indegrees[i];
        if (group[v] != group[i]) {
          const auto [_, status] = group_edges[group[v]].insert(group[i]);
          if (status)
            ++group_indegrees[group[i]];
        }
      }
      group_item_mp[group[i]].insert(i);
    }
    
    queue<int> group_que;
    vector<int> group_order;
    for (int i = 0; i < group_indegrees.size(); ++i) {
      if (!group_indegrees[i]) {
        group_que.push(i);
      }
    }
    while (!group_que.empty()) {
      const auto tp = group_que.front();
      
      group_order.push_back(tp);
      group_que.pop();
      for (const auto& g:group_edges[tp]) {
        // cout << "visit " << tp << " " << g << " " << group_indegrees[g] << endl;
        --group_indegrees[g];
        if (!group_indegrees[g])
          group_que.push(g);
      }
    }
    /*
    cout << "Group order size is " << group_order.size() << 
        " " << group_indegrees.size() << endl; */
    if (group_order.size() != group_indegrees.size()) {
      return {};
    }
    
    vector<int> ans;
    for (const auto& gp_id : group_order) {
      const auto items = group_item_mp[gp_id];
      queue<int> item_que;
      for (const auto& item : items) {
        if (!item_indegrees[item]) {
          item_que.push(item);
        }
      }
      vector<int> cur_order;
      while (!item_que.empty()) {
        const auto tp = item_que.front();
        cur_order.push_back(tp);
        item_que.pop();
        for (const auto& i : item_edges[tp]) {
          --item_indegrees[i];
          if (!item_indegrees[i] &&
             items.find(i) != items.end()) 
            item_que.push(i);
        }
      }
      if (cur_order.size() != items.size()) {
        return {};
      }
      ans.insert(ans.end(), cur_order.begin(), cur_order.end());
    }
    
    return ans;
  }
};
```

### Line Sweep