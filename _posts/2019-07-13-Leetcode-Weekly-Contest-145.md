---
layout: post
title: Leetcode Weekly Contest 145
date: 2019-07-13
Author: 来自中世界
categories: [technology]
tags: [leetcode]
comments: true
---

### 1122. Relative Sort Array

Given two arrays `arr1` and `arr2`, the elements of `arr2` are distinct, and all elements in `arr2` are also in `arr1`.

Sort the elements of `arr1` such that the relative ordering of items in `arr1` are the same as in `arr2`.  Elements that don't appear in `arr2`should be placed at the end of `arr1` in **ascending** order.

**Example 1:**

```
Input: arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
Output: [2,2,2,1,4,3,3,9,6,7,19]
```

**Constraints:**

- `arr1.length, arr2.length <= 1000`
- `0 <= arr1[i], arr2[i] <= 1000`
- Each `arr2[i]` is distinct.
- Each `arr2[i]` is in `arr1`.

**Solution:** 

> Use a hash set S2 to record arr2
>
> Then go through arr1, if the element is in arr2, then store in a unordered_map UM1. Otherwise store this element in one vector V1. 
>
> Then go through arr2, go through UM1[element in arr2] until UM[ele] is minus to 0. At the same time, push the element into the answer vector V2.
>
> Sort V1 and append it in V2, return V2.

**Code:**

```
class Solution {
public:
    vector<int> relativeSortArray(vector<int>& arr1, vector<int>& arr2) {
        unordered_map<int, int> rec;
        for (auto ele: arr1) {
            ++rec[ele];
        }
        vector<int> ans;
        vector<int> rem;
        for (auto ele: arr2) {
            while (rec[ele] > 0) {
                ans.push_back(ele);
                --rec[ele];
            }
        }
        for (auto ele:arr1) {
            while (rec[ele] > 0) {
                rem.push_back(ele);
                --rec[ele];
            } 
        }
        sort(rem.begin(), rem.end());
        for (auto ele:rem) {
            ans.push_back(ele);
        }
        return ans;
    }
};
```

### 1123. Lowest Common Ancestor of Deepest Leaves

Given a rooted binary tree, find the lowest common ancestor of its deepest leaves.

Recall that:

- The node of a binary tree is a *leaf* if and only if it has no children
- The *depth* of the root of the tree is 0, and if the depth of a node is `d`, the depth of each of its children is `d+1`.
- The *lowest common ancestor* of a set `S` of nodes is the node `A` with the largest depth such that every node in S is in the subtree with root `A`.

**Example 1:**

```
Input: root = [1,2,3]
Output: [1,2,3]
```

**Example 2:**

```
Input: root = [1,2,3,4]
Output: [4]
```

**Example 3:**

```
Input: root = [1,2,3,4,5]
Output: [2,4,5]
```

**Constraints:**

- The given tree will have between 1 and 1000 nodes.
- Each node of the tree will have a distinct value between 1 and 1000

**Solution:**

Here I use a map to record every layer TreeNode. The tree map M1 is (tree deepth, unordered_map<TreeNode\*, TreeNode\*>). The value of the  tree map is another unordered map, map from the nth depth TreeNode* to its parent TreeNode\*. From this method, we can trase back to the parent the the last layer, if all of them point to the same parent, this is the parent. 

My solution is not very clear, as I can use another unordered_map M2 to record the total (child TreeNode\*, parent TreeNode\*). As we can get the deepest lead TreeNode\* from the M1, then trace back with M2.

The computation cost of my solution is $O(n) + O(n log n) + O(n)$ is $O(n log n)$. And the space computation cost is $O(n)$.

**Code:**

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    void traseTree(map<int, unordered_map<TreeNode*, TreeNode*>>& rec, 
        TreeNode* root, TreeNode* pre_node, int cnt) {
        if (!root) {
            return;
        }
        rec[cnt][root] = pre_node;
        if (root->left) {
            traseTree(rec, root->left, root, cnt+1);
        }
        if (root->right) {
            traseTree(rec, root->right, root, cnt+1);
        }
    }
    
    
    TreeNode* lcaDeepestLeaves(TreeNode* root) {
        if (!root) {
            return nullptr;
        }
        map<int, unordered_map<TreeNode*, TreeNode*>> rec;
        traseTree(rec, root, nullptr, 0);

        int deep_len = (*rec.rbegin()).first;
        auto deep_leaf = (*rec.rbegin()).second;
        
        if (deep_leaf.size() == 1) {
            return (*deep_leaf.begin()).first;
        }
        unordered_set<TreeNode*> cur_layer;
        for (auto ele: deep_leaf) {
            cur_layer.insert(ele.second);
        }
        --deep_len;
        if (cur_layer.size() == 1) {
            return *cur_layer.begin();
        }
        while (deep_len >= 0) {
            auto new_layer = rec[deep_len];
            unordered_set<TreeNode*> tmp_layer;
            for (auto ele:cur_layer) {
                tmp_layer.insert(new_layer[ele]);
            }
            if (tmp_layer.size() == 1) {
                return *tmp_layer.begin();
            }
            cur_layer = tmp_layer;
            --deep_len;
        }
        return nullptr;
    }
};
```

### 1124. Longest Well-Performing Interval 

We are given `hours`, a list of the number of hours worked per day for a given employee.

A day is considered to be a *tiring day* if and only if the number of hours worked is (strictly) greater than `8`.

A *well-performing interval* is an interval of days for which the number of tiring days is strictly larger than the number of non-tiring days.

Return the length of the longest well-performing interval.

**Example 1:**

```
Input: hours = [9,9,6,0,6,6,9]
Output: 3
Explanation: The longest well-performing interval is [9,9,6].
```

**Constraints:**

- `1 <= hours.length <= 10000`
- `0 <= hours[i] <= 16`

**Solution:**  this problem can be abstracted as there is an array [1,1,-1,-1,-1,-1,1]. Find the longest sequential sub array with sum larger then 0. So we can solve this with DP. 

> Transform the hours into 1 or 1. It becomes [1,1,-1,-1,-1,-1,1]
>
> Calculate accumulated hours of the array A [1,2,1,0,-1,-2,-1].
>
> Calculate the potential well performing from array size to 1. As long as A[stt+len] - A[stt] is larger then 0.

**Code:**

```
class Solution {
public:
    int longestWPI(vector<int>& hours) {
        hours[0] = hours[0] > 8?1:-1;
        int cur_max = hours[0] > 0?1:0;
        for (int idx = 1; idx < hours.size(); ++idx) {
            hours[idx] = (hours[idx]>8?1:-1) + hours[idx-1];
            if (hours[idx] > 0) {
                cur_max = idx + 1;
            }
        }
        
        for (int len = hours.size()-1; len > cur_max; --len) {
            for (int stt = 0; stt < hours.size() - len; stt++) {
                if (hours[stt+len] - hours[stt] > 0) {
                    return len;
                }
            }
        }
        
        return cur_max;
    }
};
```



### Smallest Sufficient Team

Hard

In a project, you have a list of required skills `req_skills`, and a list of `people`.  The i-th person `people[i]` contains a list of skills that person has.

Consider a *sufficient team*: a set of people such that for every required skill in `req_skills`, there is at least one person in the team who has that skill.  We can represent these teams by the index of each person: for example, `team = [0, 1, 3]`represents the people with skills `people[0]`, `people[1]`, and `people[3]`.

Return **any** sufficient team of the smallest possible size, represented by the index of each person.

You may return the answer in any order.  It is guaranteed an answer exists.

**Example 1:**

```
Input: req_skills = ["java","nodejs","reactjs"], people = [["java"],["nodejs"],["nodejs","reactjs"]]
Output: [0,2]
```

**Example 2:**

```
Input: req_skills = ["algorithms","math","java","reactjs","csharp","aws"], people = [["algorithms","math","java"],["algorithms","math","reactjs"],["java","csharp","aws"],["reactjs","csharp"],["csharp","math"],["aws","java"]]
Output: [1,2]
```

**Constraints:**

- `1 <= req_skills.length <= 16`
- `1 <= people.length <= 60`
- `1 <= people[i].length, req_skills[i].length, people[i][j].length <= 16`
- Elements of `req_skills` and `people[i]` are (respectively) distinct.
- `req_skills[i][j], people[i][j][k]` are lowercase English letters.
- It is guaranteed a sufficient team exists.

 **Solution**: using one map to map from skill to int for better represent the skills. Then go through every people, there is another unordered_map with (skill_mask, people_vector). Finanlly, return the full skill mask repsonding peopel vector. 

**Code:**

```
class Solution {
public:
    vector<int> smallestSufficientTeam(
        vector<string>& req_skills, 
        vector<vector<string>>& people) 
    {
        unordered_map<string, int> skill_map;
        unordered_map<int, vector<int>> cur_rec;
        cur_rec[0] = {};

        int skill_size = req_skills.size();
        cur_rec.reserve(1 << skill_size);
        for (int idx = 0; idx < req_skills.size(); ++idx) {
            skill_map[req_skills[idx]] = idx;
        }    

        int num_people = people.size();
        for (int idx = 0; idx < num_people; ++idx) {
            // get current people skill mask
            int cur_skill = 0;
            auto cur_people = people[idx];
            for (auto skill: cur_people) {
                cur_skill |= 1 << skill_map[skill];
            }

            for (auto cur_mask = cur_rec.begin(); 
                      cur_mask != cur_rec.end(); ++cur_mask) {
                auto combo_mask = cur_mask->first | cur_skill;
                if (cur_rec.find(combo_mask) == cur_rec.end() ||
                  cur_rec[combo_mask].size() > cur_rec[cur_mask->first].size() + 1) {
                    cur_rec[combo_mask] = cur_mask->second;
                    cur_rec[combo_mask].push_back(idx);
                }    
            }
        }

        return cur_rec[(1 << skill_size) - 1];
    }
};
```



 