---
layout: post
title: graph union find
date: 2021-07-03
categories: [technology]
tags: [tech]
comments: false
---

### 并查集

- 原理篇：被称为disjoint set union或者是union find。常用的操作有：MAKE-SET(x)将一组元素弄成不相交的集合; UNION(x, y)：将元素x和元素y的集合进行合并; FIND-SET(x)：找到元素x所属的集合。
- union find的技巧加速：为了避免进行线性链表的树，使用按秩合并（union by rank）：树的高度，两个集合合并的时候，把小的合并到大的下面；相等的话可以任取一个，然后秩+1。路径压缩：就是降低树的高度，使得寻找根节点的期望时间大大降低。
- Union Find是构造最小生成树的方法，minimum spanning tree。有Kruskal和Prim。

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

// 1D union find
// 839. Similar String Groups
// Similar problem: 
// 737. Sentence Similarity II
// https://leetcode.com/problems/sentence-similarity-ii/
// 952. Largest Component Size by Common Factor
// https://leetcode.com/problems/largest-component-size-by-common-factor/
// 990. Satisfiability of Equality Equations
// https://leetcode.com/problems/satisfiability-of-equality-equations/
// 1202. Smallest String With Swaps: union the char
// https://leetcode.com/problems/smallest-string-with-swaps/
// 1101. The Earliest Moment When Everyone Become Friends
// https://leetcode.com/problems/the-earliest-moment-when-everyone-become-friends/ Sort and union find.
// 1061. Lexicographically Smallest Equivalent String
// https://leetcode.com/problems/lexicographically-smallest-equivalent-string/
// 765. Couples Holding Hands
// https://leetcode.com/problems/couples-holding-hands/
// 1722. Minimize Hamming Distance After Swap Operations
class Solution {
 public:
  int FindSet(const int num, vector<int>& record) {
    if (record[num] != num) {
      record[num] = FindSet(record[num], record);
    }
    return record[num];
  }
  
  bool Similar(const string s1, const string s2) {
    int diff = 0;
    vector<int> idx;
    for (int i = 0; i < s1.size(); ++i) {
      if (s1[i] != s2[i]) {
        ++diff;
        idx.push_back(i);
      }
      if (diff > 2) return false;
    }
    return (diff == 2 && (s1[idx[0]] == s2[idx[1]] && s1[idx[1]] == s2[idx[0]])) || !diff;
  }
  
  void Union(const int n1, const int n2, vector<int>& record,
             vector<int>& rank) {
    if (rank[n1] > rank[n2]) {
      record[n2] = n1;
    } else {
      record[n1] = n2;
      if (rank[n1] == rank[n2]) {
        ++rank[n2];
      }
    }
  }
  
  int numSimilarGroups(vector<string>& strs) {
    vector<int> record(strs.size(), 0);
    vector<int> rank(strs.size(), 0);
    for (int i = 0; i < strs.size(); ++i) 
      record[i] = i;
    int num_group = strs.size();
    for (int i = 0; i < strs.size(); ++i) {
      for (int j = 0; j < i; ++j) {
        if (Similar(strs[i], strs[j])) {
          const auto p_j = FindSet(j, record);
          const auto p_i = FindSet(i, record);
          if (p_i != p_j) {
            Union(p_i, p_j, record, rank);
            --num_group;
          }
        }
      }
    }
    return num_group;
  }
};  
// 721. Accounts Merge
class Solution {
 public:
  int FindSet(const int num, vector<int>& record) {
    if (num != record[num]) {
      record[num] = FindSet(record[num], record);
    }
    return record[num];
  }
  
  void Union(const int a1, const int a2, vector<int>& rank, 
             vector<int>& record) {
    if (rank[a1] > rank[a2]) {
      record[a2] = a1;
    } else {
      record[a1] = a2;
      if (rank[a1] == rank[a2]) {
        ++rank[a2];
      }
    }
  }
  
  vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
    vector<string> emails;
    vector<int> record;
    vector<int> rank;
    unordered_map<string, string> email_to_name;
    unordered_map<string, vector<int>> email_to_ids; 
      
    for (const auto& account : accounts) {
      const int num = record.size();
      for (int i = 1; i < account.size(); ++i) {
        emails.push_back(account[i]);
        rank.push_back(0);
        record.push_back(num);
        email_to_name[account[i]] = account[0];
        email_to_ids[account[i]].push_back(num);
      }
    }
    
    for (const auto [_, ids] : email_to_ids) {
      for (int i = 1; i < ids.size(); ++i) {
        const auto a1 = FindSet(ids[0], record);
        const auto a2 = FindSet(ids[i], record);
        Union(a1, a2, rank, record);
      }
    }
    
    unordered_map<int, unordered_set<string>> group;
    for (int i = 0; i < record.size(); ++i) {
      const auto a = FindSet(i, record);
      group[a].insert(emails[i]);
    }
    
    vector<vector<string>> ans;
    for (const auto [a1, a2] : group) {
      vector<string> account(a2.cbegin(), a2.cend());
      sort(begin(account), end(account));
      ans.push_back({email_to_name[account[0]]});
      ans.back().insert(ans.back().end(), account.begin(), account.end());
    }
    
    return ans;
  }
};

// Large space Union-Find, will calculate the result first then for the query.
// 1627. Graph Connectivity With Threshold
class Solution {
 private:
  class UnionFind {
   public:
    UnionFind(const int n, const int threshold) {
      for (int i = 0; i <= n; ++i) 
        record_.push_back(i);
      rank_ = vector<int>(n+1, 0);
      for (int t = threshold; t <=n; ++t) {
        int m = 2;
        while (m * t <= n) {
          Union(t, m*t);
          ++m;
        }
      }
    }
    
    int Find(const int num) {
      if (record_[num] != num) {
        record_[num] = Find(record_[num]);
      }
      return record_[num];
    }
    
    void Union(const int a, const int b) {
      const int p_a = Find(a);
      const int p_b = Find(b);
      if (p_a == p_b) return;
      if (rank_[p_a] > rank_[p_b]) {
        record_[p_b] = p_a;
      } else {
        record_[p_a] = p_b;
        if (rank_[p_a] == rank_[p_b]) {
          ++rank_[p_b];
        }
      }
    }
    
    bool IsInSameUnion(const int a, const int b) {
      return Find(a) == Find(b);
    }
    
    vector<int> record_;
    vector<int> rank_;
  };
 public:
  vector<bool> areConnected(int n, int threshold, vector<vector<int>>& queries) {
    vector<bool> ans;
    UnionFind uf(n, threshold+1);
    for (const auto& query : queries) {
      ans.emplace_back(uf.IsInSameUnion(query[0], query[1]));
    }
    return ans;
  }
};

// 2D union find
// Similar problem:
// 959. Regions Cut By Slashes
// https://leetcode.com/problems/regions-cut-by-slashes/
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
    int dirs[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };
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
// 778. Swim in Rising Water
class Solution {
 public:
  int FindSet(const int y, const int x, const unordered_map<int, pair<int, int>>& record, vector<vector<int>>& grid) {
    int cur = grid[y][x];
    const auto cur_pos = record.at(cur);
    if (cur_pos.first != y || cur_pos.second != x) {
      grid[y][x] = FindSet(cur_pos.first, cur_pos.second, record, grid);
    }
    return grid[y][x];
  }
  
  void Union(const int y1, const int x1, const int y2, const int x2,
             vector<vector<int>>&grid, vector<vector<int>>& rank, const unordered_map<int, pair<int, int>>& record) {
    const auto a1 = FindSet(y1, x1, record, grid);
    const auto a2 = FindSet(y2, x2, record, grid);
    if (a1 != a2) {
      const auto a1_pos = record.at(a1);
      const auto a2_pos = record.at(a2);
      if (rank[a1_pos.first][a1_pos.second] > rank[a2_pos.first][a2_pos.second]) {
        grid[a2_pos.first][a2_pos.second] = a1;
      } else {
        grid[a1_pos.first][a1_pos.second] = a2;
        if (rank[a1_pos.first][a1_pos.second] == rank[a2_pos.first][a2_pos.second]) {
          ++rank[a2_pos.first][a2_pos.second];
        }
      }
    }
  }
  
  int swimInWater(vector<vector<int>>& grid) {
    vector<vector<int>> rank(grid.size(), vector<int>(grid[0].size(), 0));
    unordered_map<int, pair<int, int>> record;
    for (int i = 0; i < grid.size(); ++i) {
      for (int j = 0; j < grid.size(); ++j) {
        record[grid[i][j]] = make_pair(i, j);
      }
    }
    const int dirs[2][4] = { {-1, 0, 1, 0}, {0, 1, 0, -1} };
    for (int i = 0; i < grid.size()*grid.size(); ++i) {
      const auto num = record[i];
      for (int j = 0; j < 4; ++j) {
        const int new_y = dirs[0][j] + num.first;
        const int new_x = dirs[1][j] + num.second;
        if (new_x >= 0 && new_x < grid.size() && new_y >= 0 && new_y < grid.size()) {
          if (grid[new_y][new_x] <= i) {
            Union(new_y, new_x, num.first, num.second, grid, rank, record);
          }
        }
      }
      const auto a1 = FindSet(0, 0, record, grid);
      const auto a2 = FindSet(grid.size()-1, grid.size()-1, record, grid);
      if (a1 == a2) return i;
    }
    return -1;
  }
};

// Graph based union find with minimum spanning tree.
// Similar problem
// 1319. Number of Operations to Make Network Connected
// Calculate the number of the connected component.
// 1135. Connecting Cities With Minimum Cost
// Sort: < 从小到大； > 从大到小
class Solution {
 public:
  int FindSet(const int num, vector<int>& record) {
    if (num != record[num]) {
      record[num] = FindSet(record[num], record);
    }
    return record[num];
  } 
  
  void Union(const int a1, const int a2, vector<int>& rank, 
             vector<int>& record) {
    if (rank[a1] > rank[a2]) {
      record[a2] = a1;
    } else {
      record[a1] = a2;
      if (rank[a1] == rank[a2]) {
        ++rank[a2];
      }
    }
  }
  
    int minimumCost(int N, vector<vector<int>>& connections) {
      sort(begin(connections), end(connections), 
           [](const vector<int>& v1, const vector<int>& v2){
        return v1[2] < v2[2];
      });  
      int num_connected = N;
      int sum_dis = 0;
      vector<int> record(N + 1, 0);
      vector<int> rank(N + 1, 0);
      for (int i = 0; i < N; ++i) record[i] = i;
      for (const auto& connection : connections) {
        const auto a1 = FindSet(connection[0], record);
        const auto a2 = FindSet(connection[1], record);
        if (a1 != a2) {
          Union(a1, a2, rank, record);
          sum_dis += connection[2];
          --num_connected;
        }
        if (num_connected == 1) break;
      }
      return num_connected == 1?sum_dis:-1;
    }
};

// 
class Solution {
 public:
  int FindSet(const int num, vector<int>& record) {
    if (num != record[num]) {
      record[num] = FindSet(record[num], record);
    }
    return record[num];
  }
  
  void Union(const int a1, const int a2, vector<int>& rank,
             vector<int>& record) {
    if (rank[a1] > rank[a2]) {
      record[a2] = a1;
    } else {
      record[a1] = a2;
      if (rank[a1] == rank[a2]) {
        ++rank[a2];
      }
    }
  }
  
  int minCostConnectPoints(vector<vector<int>>& points) {
    vector<vector<int>> edges;
    vector<int> record(points.size()+1, 0);
    for (int i = 0; i < points.size(); ++i) record[i] = i;
    
    vector<int> rank(points.size()+1, 0);
    for (int i = 0; i < points.size(); ++i) {
      for (int j = i + 1; j < points.size(); ++j) {
        const int dis = abs(points[i][0] - points[j][0]) +
                        abs(points[i][1] - points[j][1]);
        edges.push_back({i, j, dis});
      }
    }
    sort(begin(edges), end(edges), [](const vector<int>& v1,
      const vector<int>& v2) {
      return v1[2] < v2[2];
    });
    int num_components = points.size();
    int dis_sum = 0;
    for (const auto& edge : edges) {
      const auto a1 = FindSet(edge[0], record);
      const auto a2 = FindSet(edge[1], record);
      if (a1 != a2) {
        Union(a1, a2, rank, record);
        --num_components;
        dis_sum += edge[2];
      }
      if (num_components == 1) break;
    }
    return dis_sum;
  }
};

// Graph is construct firstly, then missing some edges.
// 1489. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree
class Solution {
 public:
  class UnionFind {
   public:
    UnionFind(const int n) {
      rank = vector<int>(n, 0);
      for (int i = 0; i < n; ++i) {
        record.emplace_back(i);
      }
    }
    
    void Union(const int v1, const int v2) {
      if (rank[v1] > rank[v2]) {
        record[v2] = v1;
      } else {
        record[v1] = v2;
        if (rank[v1] == rank[v2]) {
          ++rank[v2];
        }
      }
    }
    
    int FindSet(const int num) {
      if (record[num] != num) {
        record[num] = FindSet(record[num]);
      }
      return record[num];
    }
    
   private:  
    vector<int> record;
    vector<int> rank;
  }; 
  
  int CalculateMstScore(const vector<vector<int>>& edges, UnionFind& mst, const int index) {
    int score = 0;
    for (int i = 0; i < edges.size(); ++i) {
      if (i != index) {
        const int v1 = mst.FindSet(edges[i][0]);
        const int v2 = mst.FindSet(edges[i][1]);
        if (v1 != v2) {
          mst.Union(v1, v2);
          score += edges[i][2];
        }
      }
    }
    if (index >= 0 && index < edges.size()) {
      const int node1 = mst.FindSet(edges[index][0]);
      const int node2 = mst.FindSet(edges[index][1]);
      if (node1 != node2) return INT_MAX;
    }
    return score;
  }
  
  int CalculateMstScoreFirst(const vector<vector<int>>& edges, UnionFind& mst, const int index) {
    mst.Union(edges[index][0], edges[index][1]);
    return CalculateMstScore(edges, mst, index) + edges[index][2];
  }
  
  vector<vector<int>> findCriticalAndPseudoCriticalEdges(int n, vector<vector<int>>& edges) {
    for (int i = 0; i < edges.size(); ++i) {
      edges[i].push_back(i);
    }
    sort(begin(edges), end(edges), [](const vector<int>& edge1, const vector<int>& edge2) {
      return edge1[2] < edge2[2];
    });
    int mst_weight = 0;
    UnionFind mst(n);
    mst_weight = CalculateMstScore(edges, mst, -1);
    vector<vector<int>> ans(2, vector<int>());
    for (int i = 0; i < edges.size(); ++i) {
      UnionFind mst_1(n);
      int new_weight = CalculateMstScore(edges, mst_1, i);
      if (new_weight > mst_weight) {
        ans[0].push_back(edges[i][3]);
      } else if (new_weight != INT_MAX) {
        UnionFind mst_2(n);
        new_weight = CalculateMstScoreFirst(edges, mst_2, i);
        if (new_weight == mst_weight) {
          ans[1].push_back(edges[i][3]);
        }
      }
    }
    return ans;
  }
};  

// 924. Minimize Malware Spread
class Solution {  
 public:
  class UnionFind {
   public: 
    UnionFind(const int n) {
      rank_ = vector<int>(n);
      count_ = vector<int>(n, 1);
      for (int i = 0; i < n; ++i) {
        record_.emplace_back(i);
      }
    }
    
    void Union(const int v1, const int v2) {
      const auto p1 = Find(v1);
      const auto p2 = Find(v2);
      if (p1 == p2) return;
      if (rank_[p1] > rank_[p2]) {
        record_[p2] = p1; 
        count_[p1] += count_[p2]; 
      } else {
        record_[p1] = p2;
        count_[p2] += count_[p1];
        if (rank_[p1] == rank_[p2]) {
          ++rank_[p2];
        }
      }
    }
    
    int Find(const int num) {
      if (record_[num] != num) {
        record_[num] = Find(record_[num]);
      }
      return record_[num];
    }
    
    vector<int> record_;
    vector<int> rank_;
    vector<int> count_;
  };
  
  int minMalwareSpread(vector<vector<int>>& graph, vector<int>& initial) {
    UnionFind uf(graph.size());
    for (int i = 0; i < graph.size(); ++i) {
      for (int j = i + 1; j < graph.size(); ++j) {
        if (graph[i][j]) {
          uf.Union(i, j);
        }
      }
    }
    vector<int> count(graph.size(), 0);
    for (const auto v:initial) {
      ++count[uf.Find(v)];
    }
    int max_size = 0;
    int max_node = INT_MAX;
    for (const auto& v : initial) {
      const int p = uf.Find(v);
      if (count[p] == 1) {
        if (uf.count_[p] > max_size) {
          max_size = uf.count_[p];
          max_node = v;
        } else if (uf.count_[p] == max_size && v < max_node) {
          max_node = v;
        }
      }
    }
    if (max_node == INT_MAX) {
      for (const auto& v : initial) {
        max_node = min(max_node, v);
      }
    }
    return max_node;
  }
};

// 685. Redundant Connection II
class Solution {
 class UnionFind {
  public:
   UnionFind(const int n) {
     for (int i = 0; i <= n; ++i) {
       record_.emplace_back(i);
     }
   }
   
   int Find(const int num) {
     if (record_[num] != num) {
       record_[num] = Find(record_[num]);
     }
     return record_[num];
   }
   
   bool Union(const int a, const int b) {
     const int p_a = Find(a);
     const int p_b = Find(b);
     record_[p_b] = p_a;
     return p_a != p_b;
   }
   
   vector<int> record_;
 };
  
 public:
  bool DetectCycle(const int a, const int b, const vector<vector<int>>& edges) {
    UnionFind uf(edges.size());
    for (const auto edge : edges) {
      if (edge[0] == a && edge[1] ==b) 
        continue;
      const bool status = uf.Union(edge[0], edge[1]);
      if (!status) return true;
    }
    return false;
  } 
  
  vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
    UnionFind uf(edges.size());
    int node = -1;
    for (const auto& edge : edges) {
      const bool status = uf.Union(edge[0], edge[1]);
      if (!status) {
        node = uf.Find(edge[0]);
        break;
      }
    }
    unordered_map<int, vector<int>> record;
    for (int i = 1; i <= edges.size(); ++i) {
      if (uf.Find(i) == node) record[i] = {};
    }
    vector<int> last_ans;
    int target = INT_MAX;
    for (const auto& edge : edges) {
      if (record.find(edge[1]) != record.end()) {
        record[edge[1]].push_back(edge[0]);
        if (record[edge[1]].size() == 2) {
          target = edge[1];
        }
        last_ans = edge;
      }
    }
    
    if (target < 1001) {
      if (!DetectCycle(record[target][1], target, edges)) {
        return {record[target][1], target};
      } else {
        return {record[target][0], target};
      }
    }
    
    return last_ans;
  }
};

// 1168. Optimize Water Distribution in a Village
class Solution {
 private:
  class UnionFind {
   public:
    UnionFind(const int n) {
      rank_ = vector<int>(n, 0);
      num_component_ = n;
      for (int i = 0; i < n; ++i) {
        record_.push_back(i);
      }
    }
    
    int Find(const int num) {
      if (record_[num] != num) {
        record_[num] = Find(record_[num]);
      }
      return record_[num];
    }
    
    void Union(const int a, const int b, const int cost) {
      const auto p_a = Find(a);
      const auto p_b = Find(b);
      if (p_a == p_b) return;
      if (rank_[p_a] > rank_[p_b]) {
        record_[p_b] = p_a;
      } else {
        record_[p_a] = p_b;
        if (rank_[p_a] == rank_[p_b]) {
          ++rank_[p_b];
        }
      }
      cost_ += cost;
      --num_component_;
    }
    
    vector<int> rank_;
    vector<int> record_;
    int cost_ = 0;
    int num_component_ = 0;
  };
 public:
  int minCostToSupplyWater(int n, vector<int>& wells, vector<vector<int>>& pipes) {
    for (int i = 0; i < wells.size(); ++i) {
      pipes.push_back({0, i+1, wells[i]});
    }      
    sort(pipes.begin(), pipes.end(), [](const vector<int>& p1, const vector<int>& p2) {
      return p1[2] < p2[2];
    });
    UnionFind uf(n+1);
    for (const auto& pipe : pipes) {
      uf.Union(pipe[0], pipe[1], pipe[2]);
      if (uf.num_component_ == 1) break;
    }
    return uf.cost_;
  }
}

// 1631. Path With Minimum Effort
// Dijkstra's
class Solution {
 public:
  int minimumEffortPath(vector<vector<int>>& heights) {
    const int &rows = heights.size();
    const int &cols = heights.empty() ? 0 : heights[0].size();
    if (!rows || !cols) return 0;
    vector<vector<int>> efforts(rows, vector<int>(cols, INT_MAX));
    efforts[0][0] = 0;
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> search_nodes;
    search_nodes.push({0, 0});
      
    while (!search_nodes.empty()) {
      const auto tp = search_nodes.top();
      search_nodes.pop();
      const auto &r = tp.second / 100;
      const auto &c = tp.second % 100;
      if (r == rows - 1 && c == cols - 1)
        break;
      static const int dir[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };
      for (int i = 0; i < 4; ++i) {
        const auto &new_r = dir[1][i] + r;
        const auto &new_c = dir[0][i] + c;
        if (new_r < 0 || new_r >= rows || new_c < 0 || new_c >= cols) 
          continue;
        const auto new_effort = max(abs(heights[r][c] - heights[new_r][new_c]), efforts[r][c]);
        if (new_effort < efforts[new_r][new_c]) {
          efforts[new_r][new_c] = new_effort;
          search_nodes.push({new_effort, new_r*100 + new_c});
        }
      }
    }
    return efforts[rows-1][cols-1];
  }
};
// The trick here is to convert the graph into edges.
class Solution {
 private:
  class UnionFind {
   public:
    UnionFind(const int n) {
      rank_ = vector<int>(n, 0);
      cost_ = vector<int>(n, 0);
      for (int i = 0; i < n; ++i) {
        record_.push_back(i);
      }
    }
    
    int Find(const int num) {
      if (record_[num] != num) {
        record_[num] = Find(record_[num]);
      }
      return record_[num];
    } 
    
    void Union(const int a, const int b, const int cost) {
      const int p_a = Find(a);
      const int p_b = Find(b);
      if (p_a == p_b) return;
      if (rank_[p_a] > rank_[p_b]) {
        record_[p_b] = p_a;
        cost_[p_a] += cost;
      } else {
        record_[p_a] = p_b;
        if (rank_[p_a] == rank_[p_b]) {
          ++rank_[p_b];
        }
        cost_[p_b] += cost;
      }
    }
    
    vector<int> record_;
    vector<int> rank_;
    vector<int> cost_;
  };
 public:
  int minimumEffortPath(vector<vector<int>>& heights) {
    vector<vector<int>> costs;
    UnionFind uf(heights.size() * heights[0].size());
    const int row = heights.size();
    const int col = heights[0].size();
    for (int i = 0; i < row; ++i) {
      for (int j = 0; j < col; ++j) {
        if (i > 0) {
          costs.push_back({(i-1)*col + j, 
                          i*col +j, abs(heights[i][j] - heights[i-1][j])});
        }
        if (j > 0) {
          costs.push_back({i*col + j - 1, 
                          i*col +j, abs(heights[i][j] - heights[i][j-1])});
        }
      }
    }
    sort(begin(costs), end(costs), [](const vector<int>& a, const vector<int>& b) {
      return a[2] < b[2];
    });
    int cost = INT_MAX;
    const int target0 = 0;
    const int target1 = heights.size() * heights[0].size() - 1;
    for (const auto edge : costs) {
      uf.Union(edge[0], edge[1], edge[2]);
      if (uf.Find(target0) == uf.Find(target1)) {
        return edge[2];
      }
    }
    return 0;
  }
};
// 1102. Path With Maximum Minimum Value
class Solution { 
 public:
  class UnionFind {
   public: 
    UnionFind(const int n) {
      for (int i = 0; i < n; ++i) 
        record_.emplace_back(i);
      rank_ = vector<int>(n, 0);
    }
    
    int Find(const int num) {
      if (num != record_[num])
        record_[num] = Find(record_[num]);
      return record_[num];
    }
    
    void Union(const int a, const int b) {
      const int p_a = Find(a);
      const int p_b = Find(b);
      if (p_a == p_b) return;
      if (rank_[p_a] > rank_[p_b]) {
        record_[p_b] = p_a;
      } else {
        record_[p_a] = p_b;
        if (rank_[p_a] == rank_[p_b]) {
          ++rank_[p_b];
        }
      }
    }
    
    vector<int> record_;
    vector<int> rank_;
  };
  
  int maximumMinimumPath(vector<vector<int>>& A) {
    const int row = A.size();
    const int col = A[0].size();
    vector<vector<int>> edges;
    for (int i = 0; i < row; ++i) {
      for (int j = 0; j < col; ++j) {
        if (i > 0) {
          edges.push_back({(i-1)*col + j, i*col + j, min(A[i-1][j], A[i][j])});
        }
        if (j > 0) {
          edges.push_back({i*col+j-1, i*col+j, min(A[i][j-1], A[i][j])});
        }
      }
    }
    sort(begin(edges), end(edges), [](const vector<int>& a, const vector<int>& b) {
      return a[2] > b[2];
    });
    UnionFind uf(row * col);
    int max_val = 0;
    for (const auto& edge : edges) {
      if (uf.Find(0) == uf.Find(row*col-1)) {
        return max_val;
      }
      uf.Union(edge[0], edge[1]);
      max_val = edge[2];
    }
    return 0;
  }
};

```



```c++
// Problem not solved yet.
// 1697. Checking Existence of Edge Length Limited Paths
// https://leetcode.com/problems/checking-existence-of-edge-length-limited-paths/
// 1724. Checking Existence of Edge Length Limited Paths II
// https://leetcode.com/problems/checking-existence-of-edge-length-limited-paths-ii/
// 1632. Rank Transform of a Matrix
// https://leetcode.com/problems/rank-transform-of-a-matrix/
// 1579. Remove Max Number of Edges to Keep Graph Fully Traversable
// https://leetcode.com/problems/remove-max-number-of-edges-to-keep-graph-fully-traversable/
// 803. Bricks Falling When Hit
// https://leetcode.com/problems/bricks-falling-when-hit/
// 399. Evaluate Division
// https://leetcode.com/problems/evaluate-division/
// 928. Minimize Malware Spread II
// https://leetcode.com/problems/minimize-malware-spread-ii/
// 947. Most Stones Removed with Same Row or Column
// https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/
```

