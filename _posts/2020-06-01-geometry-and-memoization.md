---
layout: post
title: geometry and memoization
date: 2020-06-01
categories: [technology]
tags: [tech]
comments: false

---



### Memoization

- 329 Longest Increasing Path in a Matrix

```c++
int IncreasingPathRecord(const vector<vector<int>>& matrix, const int row, const int col, 
                         vector<vector<int>>& matrix_record) {
  if (matrix_record[row][col]) return matrix_record[row][col];
      
  static constexpr int dirs[2][4] = {{0, 1, 0, -1}, {-1, 0, 1, 0}};  
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



### Geometry

- Knowledge point:

叉乘：cross product，既可以表示面积，也可以用来计算方向。如果为0，就是共线的，如果p1 x p2为正，那么p1在p2顺时针方向，右手定则。对于三点的时候，可以平移点，计算叉乘计算方向；另外叉乘可以用来看左转还是右转，计算p02, p12的叉乘。另外判断两条线段是否相交，使用叉乘即可。叉乘可以用于对角度进行排序，后面的求凸包会用到。



线段相交：任意线段相交的情况，使用移动扫除线。将线段起点和终点，按照从左到右的顺序，从低到高的顺序排序，然后进行扫描。如果是左端点，就输入T，判断最靠近左点的y方向上下两个线段判断，是不是相交。如果是右端点，就判断，同时pop出这个segment。时间复杂度为：O(n log n)



寻找凸包：1. Graham: 找最左下的点，根据polar angle进行排序，然后使用stack存点，去除顺时针的点；2. Jarvis：选择最低的点，然后开始选择最小极角作为小一个点，以此类推。角度的排序不需要进行显示计算角度，可以使用叉乘比较。



寻找最近的点：分治 + 合并是选择最小距离的区域寻找。



- 587 Erect the Fence

```c++
vector<int> FindBottomLeft(const vector<vector<int>>& points) {
  int index = 0;
  for (int i = 1; i < points.size(); ++i) {
    if (points[i][1] < points[index][1] || (points[i][1] == points[index][1] && points[i][0] < points[index][0])) 
      index = i;  
  }  
  return points[index];  
}
    
int Distance(const vector<int>& p1, const vector<int>& p2) {
  return (p1[0]-p2[0]) * (p1[0]-p2[0]) + (p1[1]-p2[1]) * (p1[1]-p2[1]);
}  
    
int CalculateTurnDirection(const vector<int>& p1, const vector<int>& p2, const vector<int>& p3) {
  const vector<int> p12{p2[0] - p1[0], p2[1] - p1[1]};
  const vector<int> p23{p3[0] - p2[0], p3[1] - p2[1]};
    
  return p12[0]*p23[1] - p12[1]*p23[0];  
}  
    
vector<vector<int>> outerTrees(vector<vector<int>>& points) {
  if (points.size() <= 3) return points;
      
  const vector<int> start_point = FindBottomLeft(points);    
  sort(points.begin(), points.end(), [start_point, this](const vector<int>& p1, const vector<int>& p2) {
    const int dir1 = CalculateTurnDirection(start_point, p1, p2);
    const int dir2 = CalculateTurnDirection(start_point, p2, p1);  
    const int dir_diff = dir1 - dir2;
    if (!dir_diff) return Distance(start_point, p1) < Distance(start_point, p2);
    return dir_diff > 0;  
  });  
      
  {
    int left = points.size() - 2;
    while (left >= 0 && !CalculateTurnDirection(start_point, points[points.size() - 1], points[left])) {
      --left;  
    }
    ++left;
    int right = points.size() - 1;
    while (left < right) {
      swap(points[left++], points[right--]);
    }    
  }  
      
  const bool debug = false;
  if (debug) {  
    for (const auto& pt : points) {
      cout << pt[0] << " " << pt[1] << endl;
    }  
  }
      
  stack<vector<int>> cur_fence;
  cur_fence.push(points[0]);
  cur_fence.push(points[1]);
      
  for (int i = 2; i < points.size(); ++i) {
    vector<int> tp = cur_fence.top();
    cur_fence.pop();  
    while (true) {
      if (CalculateTurnDirection(cur_fence.top(), tp, points[i]) < 0) {
        if (cur_fence.size() > 1) {
          tp = cur_fence.top();
          cur_fence.pop();  
        } else {
          break;
        } 
      } else {
        cur_fence.push(tp);
        break;  
      }  
    }
    cur_fence.push(points[i]);       
  }  
      
  vector<vector<int>> ans;
  while (!cur_fence.empty()) {
    ans.push_back(cur_fence.top());
    cur_fence.pop();  
  }    
    
  if (debug) cout << "--------------------------------------\n";   
      
  return ans;
}
```

- 892 Surface Area of 3D Shapes

```c++
int surfaceArea(vector<vector<int>>& grid) {
  int sumArea = 0;
  int row = grid.size();
        
  if (!row) return sumArea;          
  int col = grid[0].size();
        
  for (int r = 0; r < row; ++r) {
    for (int c = 0; c < col; ++c) {
      int& cur_data = grid[r][c];
      if (cur_data > 0) {
        sumArea += (r == 0?cur_data:max(cur_data-grid[r-1][c], 0)) + \
                               (r == row - 1?cur_data:max(cur_data-grid[r+1][c],0)) + \
                               (c == 0? cur_data:max(cur_data-grid[r][c-1], 0)) + \
                               (c == col - 1? cur_data:max(cur_data-grid[r][c+1], 0)) + \
                                2;
      }
    }
  }
        
  return sumArea;
}
```

- 1232 Check If It Is a Straight Line

```c++
bool checkStraightLine(vector<vector<int>>& coordinates) {
  for (int i = 2; i < coordinates.size(); ++i) {
    const int x_1 = coordinates[i][0] - coordinates[0][0];
    const int y_1 = coordinates[i][1] - coordinates[0][1];
    const int x_2 = coordinates[i-1][0] - coordinates[0][0];
    const int y_2 = coordinates[i-1][1] - coordinates[0][1];
    if (x_1*y_2 != x_2*y_1)  
      return false;  
    }  
  return true;  
}
```

- 1266 Minimum Time Visiting All Points

```c++
int minTimeToVisitAllPoints(vector<vector<int>>& points) {
  int step = 0;  
      
  for (int i = 1; i < points.size(); ++i) {
    const int x_diff = points[i][0] - points[i-1][0];
    const int y_diff = points[i][1] - points[i-1][1];
    step += min(abs(x_diff), abs(y_diff)) + abs(abs(x_diff) - abs(y_diff));  
  }  
        
  return step;  
}
```

- 1401 Circle and Rectangle Overlapping

```c++
bool checkOverlap(int radius, int x_center, int y_center, int x1, int y1, int x2, int y2) {
  const int edge_x = x_center > x2? x_center - x2: (x_center >= x1?0: min(x2 - x_center, x_center - x1));
  const int edge_y = y_center > y2? y_center - y2: (y_center >= y1?0: min(y2 - y_center, y_center - y1));
      
  return edge_x*edge_x+edge_y*edge_y <= radius*radius;  
}
```

- 1453 Maximum Number of Darts Inside of a Circular Dartboard

```c++
int DistanceSqr(const vector<int>& pt1, const vector<int>& pt2) {
  return (pt1[0]-pt2[0])*(pt1[0]-pt2[0]) + (pt1[1]-pt2[1])*(pt1[1]-pt2[1]);
}
    
double DistanceSqr(const vector<int>& pt1, const vector<double>& pt2) {
  return (pt1[0]-pt2[0])*(pt1[0]-pt2[0]) + (pt1[1]-pt2[1])*(pt1[1]-pt2[1]);
}
    
int InDistance(const vector<vector<int>>& points, const vector<double> center, const int r) {
  int num = 0;
  for (const auto pt : points) {
    if (DistanceSqr(pt, center) <= r*r) ++num;
  }  
  return num;  
}
    
// Good ref:
// https://leetcode-cn.com/problems/maximum-number-of-darts-inside-of-a-circular-dartboard/solution/c-xiang-liang-suan-yuan-xin-jian-dan-yi-dong-by-sm/
// https://leetcode.com/problems/maximum-number-of-darts-inside-of-a-circular-dartboard/discuss/636372/JavaC%2B%2BPython-POJ-1981 
int numPoints(vector<vector<int>>& points, int r) {
  int max_pts = 1;
  for (int i = 0; i < points.size(); ++i) {
    for (int j = 0; j < points.size(); ++j) {
      if (i == j) continue;  
      const int d2 = DistanceSqr(points[i], points[j]);  
      if (d2 > 4 * r * r) continue;
      const vector<double> mid{(points[i][0]+points[j][0])/2.0, (points[i][1]+points[j][1])/2.0};
      const vector<double> mid_norm{(points[i][1]-points[j][1]) * sqrt(r*r - d2/4.0) / sqrt(d2), (points[j][0]-points[i][0]) * sqrt(r*r - d2/4.0) / sqrt(d2)};
      const vector<double> r_pt{mid[0]+mid_norm[0], mid[1]+mid_norm[1]};
      max_pts = max(max_pts, InDistance(points, r_pt, r));
    }
  }  
  return max_pts;  
}
```

- 1515 Best Position for a Service Centre

Geometric median/Gradient decent/ Anealing 

```c++
double CalculateDistanceError(const vector<vector<int>>& positions, const double x, const double y) {
  double error = 0.0;
  for (const auto pt : positions) {
    error += sqrt((pt[0]-x)*(pt[0]-x) + (pt[1]-y)*(pt[1]-y));
  }  
  return error;  
}
    
void UpdateCurrentPosition(const vector<vector<int>>&positions, double& x, double& y) {
  double delta_x = 0.0;
  double delta_y = 0.0;
  double delta = 0.0;
      
  for (const auto pt : positions) {
    const double denominator = sqrt((x - pt[0])*(x - pt[0]) + (y - pt[1])*(y - pt[1])) + 1.0e-20;
    delta_x += pt[0] / denominator;
    delta_y += pt[1] / denominator;
    delta += 1.0 / denominator;
  }  
  x = delta_x / delta;
  y = delta_y / delta;  
}  
    
double getMinDistSum(vector<vector<int>>& positions) {
  if (positions.size() <= 1) return 0;
    
  double x_mean = 0.0;
  double y_mean = 0.0;
  for (int i = 0; i < positions.size(); ++i) {
    x_mean += positions[i][0];
    y_mean += positions[i][1];  
  }  
  x_mean /= positions.size();
  y_mean /= positions.size();
  double cur_error = CalculateDistanceError(positions, x_mean, y_mean);
  double pre_error = INT_MAX;  
  while (abs(pre_error - cur_error) >= 1.0e-7) {
    UpdateCurrentPosition(positions, x_mean, y_mean);
    pre_error = cur_error;  
    cur_error = CalculateDistanceError(positions, x_mean, y_mean);  
  }
  return cur_error;  
}
```

- 1610 Maximum Number of Visible Points

```c++
// Sliding window
int visiblePoints(vector<vector<int>>& points, int angle, vector<int>& location) {
  int res = 0;
  vector<double> angles;
  int same_pos = 0;  
  for (const auto pt : points) {
    const int diff_x = pt[0] - location[0];
    const int diff_y = pt[1] - location[1];
    if (!diff_x && !diff_y) {
      ++same_pos;
      continue;
    }
    angles.push_back(atan2(diff_y, diff_x) * 180.0 / 3.141592653);  
  }  
  sort(angles.begin(), angles.end());
  vector<double> range_angles = angles;
  range_angles.insert(range_angles.end(), angles.begin(), angles.end());
  for (int i = angles.size(); i < range_angles.size(); ++i) range_angles[i] += 360;
  for (int i = 0, j = 0; i < range_angles.size(); ++i) {
    while (j < range_angles.size() && range_angles[j] - range_angles[i] <= angle) ++j;
    res = max(res, j - i);  
  }
  return res + same_pos;  
}
```

- 939 Minimum Area Rectangle

```c++
// Method 1: sort the column, and use map: x -> lists y. Go through the map, and remeber the visited line with x , pair y1 y2.
// Method 2: hash. Use the two points from not the same y, finding another pair of points.

int minAreaRect(vector<vector<int>>& points) {
  int pt_num = points.size();
  map<int, vector<int>> record;
  for (auto pt:points) {
    record[pt[0]].push_back(pt[1]);    
  }
        
  unordered_map<int, int> line;
  int square = INT_MAX;
        
  for (auto pts: record) {
    auto pts_y = pts.second;
    int pts_num = pts_y.size();
    int x = pts.first;
    for (int idx1 = 0; idx1 < pts_num - 1; ++idx1) {
      for (int idx2 = idx1 + 1; idx2 < pts_num; ++idx2) {
        const int y1 = min(pts_y[idx1], pts_y[idx2]);
        const int y2 = max(pts_y[idx1], pts_y[idx2]);
        const int tag = y1*40001+y2;
        if (line.find(tag)!=line.end()) {
          int cur_squ = (x - line[tag])*(y2 - y1);
          square = min(cur_squ, square);
        }
        line[tag] = x;
      } 
    }
  }
        
  return square==INT_MAX?0:square;
}
```

- 963 Minimum Area Rectangle II

```c++
int getRadius(vector<int> p1, vector<int>p2) {
  return (p1[0]-p2[0])*(p1[0]-p2[0]) + (p1[1]-p2[1])*(p1[1]-p2[1]);    
}
    
long long getCenter(vector<int>p1, vector<int>p2) {
  return ((long long)(p1[0] + p2[0]))*80002 + p1[1] + p2[1]; 
}
    
vector<int> getPt(int num) {
  return vector<int>({num/40001, num%40001});
}
    
double getDistance(vector<int> p1, vector<int> p2) {
  auto cur_num = (p1[0]-p2[0])*(p1[0]-p2[0]) +
                 (p1[1]-p2[1])*(p1[1]-p2[1]);
        
  return sqrt(cur_num);
}
    
double getArea(vector<long long>c, int p1, int p2) {
  auto p1_ = getPt(p1);
  auto p2_ = getPt(p2);
        
  vector<int> p3 = vector<int>({c[0] - p2_[0], c[1] - p2_[1]});
        
  double d1 = getDistance(p1_, p3);
  double d2 = getDistance(p1_, p2_);
        
  return d1 * d2;
}
    
double minAreaFreeRect(vector<vector<int>>& points) {
  int num_pts = points.size();
        
  unordered_map<int, unordered_map<long long, vector<int>> > record;
        
  for (int idx1 = 0; idx1 < num_pts - 1; ++idx1) {
    for (int idx2 = idx1 + 1; idx2 < num_pts; ++idx2) {
      auto r = getRadius(points[idx1], points[idx2]);
      auto center = getCenter(points[idx1], points[idx2]);
      int p = points[idx1][0]*40001 + points[idx1][1];
                
      if (record.count(r) > 0) {
        auto& rec = record[r];
        rec[center].push_back(p);
      }
      else {
        unordered_map<long long, vector<int>> tmp;
        tmp[center].push_back(p);
        record[r] = tmp;
      }
    }
  }
        
  double area = INT_MAX;
  for (auto rec:record) {
    int r = rec.first;
    auto& center_p = rec.second;
    for (auto ele:center_p) {
      auto& c = ele.first;
      auto& pts = ele.second;
      vector<long long> center({c/80002, c%80002});
      int cur_len = pts.size();
      for (int idx = 0; idx < cur_len - 1; ++idx) {
        for (int idx2 = idx + 1; idx2 < cur_len; ++idx2) {
          auto cur_area = getArea(center, pts[idx], pts[idx2]);
          area = min(cur_area, area);
        }
      }
    }
  }
  return area == INT_MAX?0:area;
}
```





 