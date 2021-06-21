---
layout: post
title: Alphabet Verily interview preparation
date: 2019-09-01
categories: [technology]
tags: [coding]
comments: false
---



Prepare for the **Senior Software Engineer, Surgical Devices (Mountain View, CA)** 



```c++
const volatile int i = 10;

// 没有问题
// volatile: 存在寄存器里面的值，表示会被意想不到的改变
// const: 表明程序不要去试图修改它。表示运行时候所在作用域内不能进行修改

// 190. Reverse Bits
uint32_t reverseBits(uint32_t n) {
  uint32_t res = 0;
  int power = 31;
  while (n > 0) {
    res |= (n & 1) << power;
    --power;
    n = n >> 1;
  }
  return res;
}
```



```c++
// Minimum Spanning Tree: 最小生成树
// 村庄供水
// 1584. Min Cost to Connect All Points
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

// 1135. Connecting Cities With Minimum Cost
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
};
```



```c++
// Knapsack: 背包问题
// 播放广告: 利润最大化；还可以加入混合背包
// 518. Coin Change 2
```



```c++
// map：map快速操作
// 355. Design Twitter

```



```c++
// Topological sort: 拓扑排序
// For multiple start points with flooding with priority queue.
vector<int> FloodingWithPriority() const {
  store the existing node into pq.
  while (!pq.empty()) {
    fetch & pop
    go the 4 or 8 connected  
  }  
  return result.
}
```



```c++
// Travel different ways
int TravelWays(const int N) {
  if (N < 2) return 0;
  int num = 0;
  vector<int> stations(N+1, 0);
  stations[1] = 1;
  for (int i = 2; i <= N; ++i) {
    for (int j = 1; j < i; ++j) {
      stations[i] += stations[j];
    }
  }
  
  return stations[N];
}
// easier way, only 2^(N-2), as we could select this station or not.
```

