---
layout: post
title: union find and ordered map
date: 2020-06-06
categories: [technology]
tags: [tech]
comments: false
---

### 并查集

- 原理篇：被称为disjoint set union或者是union find。常用的操作有：MAKE-SET(x)将一组元素弄成不相交的集合; UNION(x, y)：将元素x和元素y的集合进行合并; FIND-SET(x)：找到元素x所属的集合。
- union find的技巧加速：为了避免进行线性链表的树，使用按秩合并（union by rank）：树的高度，两个集合合并的时候，把小的合并到大的下面；相等的话可以任取一个，然后秩+1。路径压缩：就是降低树的高度，使得寻找根节点的期望时间大大降低。

```c++
// Make Set
MAKE-SET(x)
  x.p = x
  x.rank = 0

// Union By Rank
UNION(x, y)
  LINK(FIND-SET(x), FIND-SET(y))
LINK(x, y)
  if x.rank > y.rank
    y.p = x
  else x.p = y
  if x.rank == y.rank
    y.rank = y.rank + 1

// Path Compression
FIND-SET(x)
  if x != x.p
    x.p = FIND-SET(x.p)
  return x.p  
    
// 261. Graph Valid Tree
// Same problem: 
// 547. Number of Provinces: https://leetcode.com/problems/number-of-provinces/
// 323. Number of Connected Components in an Undirected Graph
// https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/
// 684. Redundant Connection https://leetcode.com/problems/redundant-connection/
class Solution {
 public:
  int FindSet(const int vertex, vector<int>& record) {
    if (vertex != record[vertex]) {
      record[vertex] = FindSet(record[vertex], record);
    }
    return record[vertex];
  }
  
  bool validTree(int n, vector<vector<int>>& edges) {
    if (n <= 0 || edges.size() < n-1) return false;
    vector<int> root(n, 0);
    vector<int> rank(n, 0);
    for (int i = 0; i < n; ++i) root[i] = i;
    int num_connected = n;
    for (const auto& edge : edges) {
      const auto a1 = FindSet(edge[0], root);
      const auto a2 = FindSet(edge[1], root);
      if (a1 == a2) return false;
      if (rank[a1] > rank[a2]) {
        root[a2] = a1;
      } else {
        root[a1] = a2;
      }
      if (rank[a1] == rank[a2]) {
        ++rank[a2];
      }
      --num_connected;
    }
    
    return num_connected == 1;
  }
};

// 2D union find
// 305. Number of Islands II
class Solution {
 public:
  int FindSet(const int x, const int y, vector<vector<int>>& record, const int m, const int n) {
    const int p_x = record[y][x] % n;
    const int p_y = record[y][x] / n;
    int new_p = y*n + x;
    if (x != p_x || y != p_y) {
      new_p = FindSet(p_x, p_y, record, m, n);
      record[y][x] = new_p;
    }
    return new_p;
  }
  
  void Union(const int a1, const int a2, const int m, const int n,
             vector<vector<int>>& rank, vector<vector<int>>& record) {
    if (rank[a1/n][a1%n] > rank[a2/n][a2%n]) {
      record[a2/n][a2%n] = a1;
    } else {
      record[a1/n][a1%n] = a2;
    }
    if (rank[a1/n][a1%n] == rank[a2/n][a2%n]) {
      ++rank[a2/n][a2%n];
    }
  }
  
  vector<int> numIslands2(int m, int n, vector<vector<int>>& positions) {
    vector<vector<int>> record(m, vector<int>(n, -1));
    vector<vector<int>> ranks(m, vector<int>(n, 0));
      
    int num_connected = 0;
    // [x, y]
    int dirs[2][4] = {{0, 1, 0, -1}, {-1, 0, 1, 0}};
    vector<int> ans;
      
    for (const auto& position : positions) {
      if (record[position[0]][position[1]] >= 0) {
        ans.push_back(num_connected);
        continue;
      }
      ++num_connected;
      const int key = n * position[0] + position[1];
      record[position[0]][position[1]] = key;
      for (int i = 0; i < 4; ++i) {
        const int new_x = dirs[0][i] + position[1];
        const int new_y = dirs[1][i] + position[0];
        if (new_x >= 0 && new_x < n && new_y >= 0 && new_y < m && record[new_y][new_x] >= 0) {
          const int a1 = FindSet(new_x, new_y, record, m, n);
          const int a2 = FindSet(position[1], position[0], record, m, n);
          if (a1 != a2) {
            --num_connected;
            Union(a1, a2, m, n, ranks, record);
          }
        }
      }
      ans.push_back(num_connected);
    }
    return ans;
  }
};


```

