---
layout: post
title: Baidu interview preparation
date: 2019-09-03
categories: [technology]
tags: [coding]
comments: false
---



For the projects, see the DL/ML summary; CUDA summary; multi-threading.



For the coding part, see the below:

```c++
// 149. Max points on a Line
bool SameLine(const vector<int>& p1,
              const vector<int>& p2,
              const vector<int>& p3) {
  const int p21_x = p2[1] - p1[1];
  const int p21_y = p2[0] - p1[0];
  const int p31_x = p3[1] - p1[1];
  const int p31_y = p3[0] - p1[0];
  return p21_x * p31_y == p21_y * p31_x;
}  
  
int maxPoints(vector<vector<int>>& points) {
  int res = 1;
  
  for (int i = 0; i < points.size(); ++i) {
    for (int j = i + 1; j < points.size(); ++j) {
      int cur_len = 2;
      for (int k = j + 1; k < points.size(); ++k) {
        if (SameLine(points[i], points[j], points[k])) {
          ++cur_len;
        }
      }
      res = max(res, cur_len);
    }
  }
  
  return res;
}
```



```c++
// 74. Search in 2D matrix
bool searchMatrix(vector<vector<int>>& matrix, int target) {
  if (target < matrix[0][0] || target > matrix.back().back())
    return false;
  vector<int> column;
  for (int i = 0; i < matrix.size(); ++i) {
    column.emplace_back(matrix[i][0]);
  }
  auto it = lower_bound(begin(column), end(column), target);
  if (it == end(column) || (*it > target && it != begin(column)))
    --it;
  const int idx = it - begin(column);
  it = lower_bound(begin(matrix[idx]), end(matrix[idx]), target);
  return it != end(matrix[idx]) &&  *it == target;
}
```



```c++
// 965. Univalued Binary Tree
bool isUnivalTree(TreeNode* root, const int val = 100) {
  if (!root) return true;
  if (val != 100 && root->val != val) return false;
  return isUnivalTree(root->left, root->val) && isUnivalTree(root->right, root->val);
}
```



```c++
// Leetcode basic calculator series
// 224. Basic Calculator
pair<string, int> Parse(const string& s) {
  int new_idx = 0;
  stack<int> stk;
  int val = 0;
  int inc_size = 0;
  string res;
  string tmp = s;
  
  while (new_idx < tmp.size()) {
    if (tmp[new_idx] == '(')
      stk.push(new_idx);
    else if (tmp[new_idx] == ')') {
      const int left = stk.top();
      stk.pop();
      val = calculate(tmp.substr(left+1, new_idx - left - 1));
      const auto new_val = to_string(val);
      tmp = tmp.substr(0, left) + new_val + tmp.substr(new_idx + 1);
      const int tmp_inc_size = new_idx - left + 1 - new_val.size();
      new_idx -= tmp_inc_size;
      inc_size += tmp_inc_size;  
      res += new_val;
    } else {
      res += tmp[new_idx];
    }
    if (stk.empty())
      break;
    ++new_idx;
  }
  
  return { tmp.substr(0, new_idx+1), new_idx + inc_size};
}  
  
int calculate(string s) {
  int res = 0;
  char op = '+';
  int tmp = 0;
  bool has_num = false;
  
  for (int i = 0; i < s.size(); ++i) {
    if (s[i] == '(') {
      const auto [str, index] = Parse(s.substr(i));
      tmp += calculate(str);
      i += index;
      has_num = true;
    } else if (s[i] >= '0' && s[i] <= '9') {
      tmp = tmp*10 - '0' + s[i];
      has_num = true;
    } else if (s[i] == ' '){
      continue;
    } else {
      const bool is_con = !has_num?true:false;
      res += op == '-'? -1 * tmp : tmp;
      tmp = 0;
      op = is_con? (s[i] == op?'+':'-') : s[i] == '-'?'-':'+';
      has_num = false;
    }
  }
  res += op == '-'? -1 * tmp : tmp;
  
  return res;
}

// 227. Basic Calculator II
int calculate(string s) {
  int s_len = s.size();      
  int cur_res = 0;
  int fin_res = 0;
  char ope = '+';
  int cur_num = 0;  
  for (int idx = 0; idx < s_len; ++idx) {
     if (isdigit(s[idx])) {
       cur_num = cur_num*10 + (s[idx] - '0');
     }
     if (s[idx] == '*' || s[idx] == '/' || s[idx] == '+' 
          || s[idx] == '-' || idx == s_len - 1) {
        switch(ope) {
          case '+': cur_res += cur_num; break;
          case '-': cur_res -= cur_num; break;
          case '*': cur_res *= cur_num; break;
          case '/': cur_res /= cur_num; break;    
       }
                
       if (s[idx] == '-' || s[idx] == '+' || s_len - 1 == idx) {
         fin_res += cur_res;
         cur_res = 0;                   
       }           
       cur_num = 0;
       ope = s[idx];
     }
  }
  return fin_res;
}

// 772. Basic Calculator III
string parserString (string s, int idx, int & new_idx) {
  int num_brance = 1;
  string str = "";      
  for (int i = idx; i < s.size(); ++i) {
    if (s[i] == '(') {
      ++num_brance;
    }
    else if (s[i] == ')') {
      --num_brance;
    }        
    if (!num_brance) {
      new_idx = i;
      return str;
    }        
    str += s[i];
  }      
  return str;
}
    
int calculate(string s) {
  int s_len = s.size();
  long long cur_num = 0;
  int cur_res = 0;
  int fin_res = 0;
  char ope = '+';      
  for (int idx = 0; idx < s_len; ++idx) {
    if (s[idx] == '(') {
      int new_idx = 0;
      string str = parserString(s, idx+1, new_idx);
      cur_num = calculate(str);
      idx = new_idx;
    }        
    if (isdigit(s[idx])) {
      cur_num = cur_num*10 + (s[idx] - '0');
    }        
    if (s[idx] == '+' || s[idx] == '-' || s[idx] == '*' ||
      s[idx] == '/' || s_len == idx + 1) {
      switch(ope) {
        case '+': cur_res += cur_num; break;
        case '-': cur_res -= cur_num; break;
        case '*': cur_res *= cur_num; break;
        case '/': cur_res /= cur_num; break;    
      }         
      if (s[idx] == '-' || s[idx] == '+' || s_len - 1 == idx) {
        fin_res += cur_res;
        cur_res = 0;
      }
      ope = s[idx];
      cur_num = 0;
    }
  }   
  return fin_res;
}
```



```c++
// 51. N-Queens
static constexpr int kNum = 10;
char kQueen[kNum][kNum];
bool row[kNum] = {false};
bool col[kNum] = {false};  
bool dg[2*kNum] = {false};
bool udg[2*kNum] = {false};

void StoreResult(const int N, vector<vector<string>>& ans) {
  vector<string> sol;
  for (int i = 0; i < N; ++i) {
    string tmp;
    for (int j = 0; j < N; ++j) {
      tmp += kQueen[i][j];
    }
    sol.push_back(tmp);
  }
  ans.push_back(sol);
}  
  
void DFS(int x, int y, 
         const int n, const int N,
        vector<vector<string>>& ans) {
  if (n > N) return;
  if (y == N) {
    y = 0;
    ++x;
  }
  if (x == N) {
    if (n == N) {
      StoreResult(N, ans);
    }
    return;
  }
  kQueen[x][y] = '.';
  DFS(x, y+1, n, N, ans);
  if (!row[y] && !col[x] && !dg[x+y] && !udg[-y+x+N]) {
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = true;
    kQueen[x][y] = 'Q';
    DFS(x, y + 1, n+1, N, ans);
    row[y] = col[x] = dg[x+y] = udg[-y+x+N] = false;
    kQueen[x][y] = '.';
  }
}  
vector<vector<string>> solveNQueens(int n) {
  vector<vector<string>> ans;
  DFS(0, 0, 0, n, ans);
  return ans;
}

// Geometry part
```

