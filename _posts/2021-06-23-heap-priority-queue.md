---
layout: post
title: heap priority queue
date: 2021-06-23
categories: [technology]
tags: [tech]
comments: false

---



### Heap

```c++
// 1882. Process Tasks Using Servers
// Using tasks only
struct Service {
  int release_time = INT_MAX;
  int service_id = INT_MAX;
  int service_w = INT_MAX;
  Service(const int t, const int s_id, const int w) {
    release_time = t;
    service_id = s_id;
    service_w = w;
  }
  bool operator< (const Service& t) const {
    return release_time > t.release_time || (release_time == t.release_time && service_w > t.service_w);
  }
};

class ServiceCompare {
  public:
    bool operator() (const pair<int, int>& num1, const pair<int, int>& num2) const {
      return num1.first > num2.first || (num1.first == num2.first && num1.second > num2.second);
    }
};  
  
vector<int> assignTasks(vector<int>& servers, vector<int>& tasks) {
  priority_queue<pair<int, int>, vector<pair<int, int>>, ServiceCompare> empty_servers;
  priority_queue<Service> used_service;
  
  for (int i = 0; i < servers.size(); ++i) {
    empty_servers.push({servers[i], i});
  }
  
  vector<int> ans(tasks.size(), 0);
  for (int i = 0; i < tasks.size(); ++i) {
    while (!used_service.empty() && used_service.top().release_time <= i) {
      const auto task = used_service.top();
      used_service.pop();
      empty_servers.push({servers[task.service_id], task.service_id});
    }
    if (!empty_servers.empty()) {
      const auto task = empty_servers.top();
      empty_servers.pop();
      used_service.push({i + tasks[i], task.second, task.first});
      ans[i] = task.second;
    } else {
      const auto task = used_service.top();
      used_service.pop();
      used_service.push({task.release_time + tasks[i], task.service_id, task.service_w});
      ans[i] = task.service_id;
    }
  }
  return ans;
}

// TLE version.
struct Task {
  int release_time = INT_MAX;
  int service_id = INT_MAX;
  int task_id = INT_MAX;
  Task(const int t, const int s_id, const int t_id) {
    release_time = t;
    service_id = s_id;
    task_id = t_id;
  }
};

class ServiceCompare {
  public:
    bool operator() (const vector<int>& num1, const vector<int>& num2) const {
      return num1[0] > num2[0] || (num1[0] == num2[0] && num1[1] > num2[1]);
    }
};

class TaskCompare {
  public:
    bool operator() (const vector<int>& task1, const vector<int>& task2) {
      return task1[0] > task2[0] || (task1[0] == task2[0] &&
                                              task1[2] > task2[2]);
    }
};  
  
vector<int> assignTasks(vector<int>& servers, vector<int>& tasks) {
  priority_queue<vector<int>, vector<vector<int>>, ServiceCompare> servers_records;
  priority_queue<vector<int>, vector<vector<int>>, TaskCompare> tasks_records;;
  
  for (int i = 0; i < servers.size(); ++i) {
    servers_records.push(vector<int>({servers[i], i}));
  }
  
  int count = 0;
  int task_idx = 0;
  vector<int> ans(tasks.size(), 0);
  while (!tasks_records.empty() || task_idx < tasks.size()) {
    while (!servers_records.empty() && task_idx < tasks.size()) {
      // cout << servers_records.size() << " " << task_idx << endl;
      const auto server = servers_records.top();
      servers_records.pop();
      tasks_records.push(vector<int>({count + tasks[task_idx], server[1], task_idx}));
      ++task_idx;
      if (task_idx < tasks.size() && count >= task_idx) continue;
      else break;
    }
    ++count;
    if (servers_records.empty() && !tasks_records.empty()) {
      const auto tp_task = tasks_records.top();
      count = max(tp_task[0], count);
    }
    while (!tasks_records.empty()) {
      const auto tp_task = tasks_records.top();
      if (tp_task[0] <= count) {
        tasks_records.pop();
        ans[tp_task[2]] = tp_task[1];
        servers_records.push({servers[tp_task[1]], tp_task[1]});
      } else {
        break;
      }
    }
  }
  return ans;
}
```



```c++
// 1675. Minimize Deviation in Array
class Solution {
 public:
  int minimumDeviation(vector<int>& nums) {
    priority_queue<int> record;
    int min_val = INT_MAX;
    int max_val = INT_MIN;
    int deviation = INT_MAX;
    for (const auto& num : nums) {
      const int new_num = (num & 1) ? num << 1 : num;
      record.push(new_num);
      min_val = min(new_num, min_val);
      max_val = max(new_num, max_val);
    }
    while (!record.empty() && !(record.top() & 1)) {
      deviation = min(deviation, record.top() - min_val);
      int tp = record.top() / 2;
      record.pop();
      record.push(tp);
      min_val = min(min_val, tp);
    }
    deviation = min(deviation, record.top() - min_val);
    return deviation;
  }
};
```

