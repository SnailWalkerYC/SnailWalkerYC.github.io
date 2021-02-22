---
layout: post
title: graph DFS backtracking and recursion and memoization
date: 2021-07-02
categories: [technology]
tags: [tech]
comments: false
---

### 

### Memoization

- 329 Longest Increasing Path in a Matrix

```c++
int IncreasingPathRecord(const vector<vector<int>>& matrix, const int row, const int col, 
                         vector<vector<int>>& matrix_record) {
  if (matrix_record[row][col]) return matrix_record[row][col];
      
  static constexpr int dirs[2][4] = { {0, 1, 0, -1}, {-1, 0, 1, 0} };  
  for (int i = 0; i < 4; ++i) {
    const int new_row = dirs[1][i] + row;
    const int new_col = dirs[0][i] + col;
    int neighbor = 0;  
    if (new_row >= 0 && new_row < matrix.size() && new_col >= 0 && new_col < matrix[0].size()
        && matrix[row][col] > matrix[new_row][new_col]) {  
      neighbor = IncreasingPathRecord(matrix, new_row, new_col, matrix_record);  
    }
    matrix_record[row][col] = max(neighbor + 1, matrix_record[row][col]);      
  }  
  return matrix_record[row][col];  
}
    
int LongestIncreasingPath(vector<vector<int>>& matrix) {
  if (matrix.empty() || matrix[0].empty()) return 0;
      
  const int row = matrix.size();
  const int col = matrix[0].size();
  vector<vector<int>> matrix_record(row, vector<int>(col, 0));
  int max_len = 0;
      
  for (int i = 0; i < row; ++i) {
    for (int j = 0; j < col; ++j) {
      if (!matrix_record[i][j]) {
        matrix_record[i][j] = IncreasingPathRecord(matrix, i, j, matrix_record);
        max_len = max(max_len, matrix_record[i][j]);  
      }
    }
  }  
        
  return max_len;  
}
```

