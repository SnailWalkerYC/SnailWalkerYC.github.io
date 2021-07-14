---
layout: post
title: Tiktok interview preparation
date: 2019-09-06
categories: [technology]
tags: [coding]
comments: false
---



```c++
// 160. Intersection of Two Linked Lists
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
  ListNode* a = headA;
  ListNode* b = headB;
  while (a && b) {
    a = a->next;
    b = b->next;
  }
  if (a) {
    while (a) {
      headA = headA->next;
      a = a->next;
    }
  }
  if (b) {
    while (b) {
      headB = headB->next;
      b = b->next;
    }
  }
  while (headA && headB) {
    if (headA == headB)
      return headA;
    headA = headA->next;
    headB = headB->next;
  }
  return nullptr;
}

// 72. Edit Distance
int minDistance(string word1, string word2) {
  vector<vector<int>> record(word1.size() + 1, 
                             vector<int>(word2.size() + 1, 0));
  word1 = "#" + word1;
  word2 = "#" + word2;
  for (int i = 1; i < word1.size(); ++i) 
    record[i][0] = i;
  for (int i = 1; i < word2.size(); ++i)
    record[0][i] = i;
  for (int i = 1; i < word1.size(); ++i) {
    for (int j = 1; j < word2.size(); ++j) {
      record[i][j] = min(record[i-1][j-1] + ((word1[i] == word2[j])?0:1), 
                         min(record[i][j-1] + 1, record[i-1][j] + 1));
    }
  }
  return record[word1.size()-1][word2.size()-1];
}

// 15. 3Sum
// n^2
vector<vector<int>> threeSum(vector<int>& nums) {
  int last_num = INT_MAX;
  vector<vector<int>> ans;
  sort(begin(nums), end(nums));
  for (int i = 0; i < nums.size(); ++i) {
    if (last_num == nums[i]) {
      continue;
    }
    const int target = -nums[i];
    unordered_map<int, int> record;
    if (target < 2 * nums[i]) {
      last_num = nums[i];
      continue;
    }
    for (int j = i + 1; j < nums.size(); ++j) {
      if (record[nums[j]] > 0 && record[target - nums[j]] > 0 &&
          2* nums[j] != target) continue; 
      if (target == 2*nums[j] && record[nums[j]] >= 2)
        continue;
      if (record[target - nums[j]] > 0) {
        ans.push_back({nums[i], target - nums[j], nums[j]});
      }
      ++record[nums[j]];
    }
    last_num = nums[i];
  }
  return ans;
}
// call two sum
vector<vector<int>> threeSum(vector<int>& nums) {
  sort(begin(nums), end(nums));
  vector<vector<int>> res;
  for (int i = 0; i < nums.size() && nums[i] <= 0; ++i)
    if (i == 0 || nums[i - 1] != nums[i]) {
      twoSumII(nums, i, res);
  }
  return res;
}
void twoSumII(vector<int>& nums, int i, vector<vector<int>> &res) {
  int lo = i + 1, hi = nums.size() - 1;
  while (lo < hi) {
    int sum = nums[i] + nums[lo] + nums[hi];
    if (sum < 0) {
      ++lo;
    } else if (sum > 0) {
      --hi;
    } else {
      res.push_back({ nums[i], nums[lo++], nums[hi--] });
      while (lo < hi && nums[lo] == nums[lo - 1])
        ++lo;
    }
  }
}

// 322. Coin Change
int coinChange(vector<int>& coins, int amount) {
  vector<int> dp(amount + 1, INT_MAX);
  dp[0] = 0;
  for (int i = 1; i <= amount; ++i) {
    for (const auto& c : coins) {
      if (i - c >= 0 && dp[i-c] != INT_MAX) {
        dp[i] = min(dp[i], dp[i-c] + 1);
      }
    }
  }
  return dp[amount] == INT_MAX?-1:dp[amount];
}

// 518. Coin Change 2(Knapsack complete)
int change(int amount, vector<int>& coins) {
  vector<int> dp(amount + 1, 0);
  dp[0] = 1;
  for (const auto& c: coins) {
    for (int i = c; i <= amount; ++i) {
      dp[i] += dp[i-c];
    }
  }
  return dp[amount];
}

// 1561. Maximum Number of Coins You Can Get
int maxCoins(vector<int>& piles) {
  int ans = 0;
  sort(begin(piles), end(piles), greater<int>());
  for (int i = 1; i < piles.size() * 2/3; i += 2) 
    ans += piles[i];
  return ans;
}

// 138. Copy List with Random Pointer
Node* copyRandomList(Node* head) {
  unordered_map<Node*, Node*> record;
  record[nullptr] = nullptr;
  Node* new_head = new Node(0);
  auto copy_cur = new_head;
  auto cur = head;
  while (cur) {
    copy_cur->next = new Node(cur->val);
    copy_cur = copy_cur->next;
    record[cur] = copy_cur;
    cur = cur->next;
  }
  cur = head;
  copy_cur = new_head->next;
  while (cur) {
    copy_cur->random = record[cur->random];
    copy_cur = copy_cur->next;
    cur = cur->next;
  }
  return new_head->next;
}
// https://leetcode.com/problems/copy-list-with-random-pointer/discuss/43497/2-clean-C%2B%2B-algorithms-without-using-extra-arrayhash-table.-Algorithms-are-explained-step-by-step.

// 416. Partition Equal Subset Sum
// 0-1 Knapsack
bool canPartition(vector<int>& nums) {
  int left = 0;
  int right = 0;
  int ttl_sum = 0;
  ttl_sum = std::accumulate(nums.begin(),nums.end(), ttl_sum);
  if(ttl_sum%2)
    return false;
  ttl_sum = ttl_sum >> 1;
  vector<bool> rec(ttl_sum+1, false);
  rec[0] = true;
  for(auto num:nums) {
    for(int cur=ttl_sum; cur>=num; cur--) 
      rec[cur] = rec[cur] || rec[cur-num];
    if(rec[ttl_sum])
      return true;
  }
  return rec[ttl_sum];
}

// 698. Partition to K Equal Sum Subsets
// NP-hard 
// Sorted from large to small
// nums[i] == nums[i-1], nums[i] fail -> nums[i-1] fail.

// 351. Android Unlock Patterns 
```

