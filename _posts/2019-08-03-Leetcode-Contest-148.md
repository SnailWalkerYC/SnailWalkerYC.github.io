---
layout: post
title: Leetcode Weekly Contest 148
date: 2019-08-03
Author: Snail Walker
categories: [technology]
tags: [coding]
comments: true
---



#### 1144. Decrease Elements To Make Array Zigzag

Given an array `nums` of integers, a *move* consists of choosing any element and **decreasing it by 1**.

An array `A` is a *zigzag array* if either:

- Every even-indexed element is greater than adjacent elements, ie. `A[0] > A[1] < A[2] > A[3] < A[4] > ...`
- OR, every odd-indexed element is greater than adjacent elements, ie. `A[0] < A[1] > A[2] < A[3] > A[4] < ...`

Return the minimum number of moves to transform the given array `nums` into a zigzag array.

 

**Example 1:**

```
Input: nums = [1,2,3]
Output: 2
Explanation: We can decrease 2 to 0 or 3 to 1.
```

**Example 2:**

```
Input: nums = [9,6,1,6,2]
Output: 4
```

- Thought

  Make the $a_{i-2}$ > $a_{i-1}$ < $a_i$. The decreasing is $max (0, min(a_{i-1}, a_i) - a_{i-1} + 1 )$. 

- Code

  ```
  class Solution {
  public:
      int movesToMakeZigzag(vector<int>& nums) {
          int even_idx = 0;
          int odd_idx = 0;
          int nums_len = nums.size();
          int even_num = nums[0];
          int odd_num = nums[0];
          for (int idx = 1; idx < nums_len; ++idx) {
              if (idx&1) {
                  // even 
                  if (nums[idx] >= even_num) {
                      even_idx += nums[idx] - even_num + 1;
                      --even_num;
                  }
                  else {
                      even_num = nums[idx];
                  }
                  if (nums[idx] <= odd_num) {
                      odd_idx += odd_num - nums[idx] + 1;
                  }
                  odd_num = nums[idx];
              }
              else {
                  // odd
                  if (nums[idx] >= odd_num) {
                      odd_idx += nums[idx] - odd_num + 1;
                      --odd_num;
                  }
                  else {
                      odd_num = nums[idx];
                  }
                  if (nums[idx] <= even_num) {
                      even_idx += even_num - nums[idx] + 1;
                  }
                  even_num = nums[idx];                
              }
          }
          
          return min(even_idx, odd_idx);
      }
  };
  ```

  

#### 1145. Binary Tree Coloring Game

Two players play a turn based game on a binary tree.  We are given the `root` of this binary tree, and the number of nodes `n` in the tree.  `n` is odd, and each node has a distinct value from `1` to `n`.

Initially, the first player names a value `x` with `1 <= x <= n`, and the second player names a value `y` with `1 <= y <= n` and `y != x`.  The first player colors the node with value `x` red, and the second player colors the node with value `y` blue.

Then, the players take turns starting with the first player.  In each turn, that player chooses a node of their color (red if player 1, blue if player 2) and colors an **uncolored** neighbor of the chosen node (either the left child, right child, or parent of the chosen node.)

If (and only if) a player cannot choose such a node in this way, they must pass their turn.  If both players pass their turn, the game ends, and the winner is the player that colored more nodes.

You are the second player.  If it is possible to choose such a `y` to ensure you win the game, return `true`.  If it is not possible, return `false`.

- Thought

   Calculate the left and right number of target node, and the remaining can be got. So if the rem or the max of left or right is larger than the other part, it will be true.

- Code

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
    bool found = false;
    
    int getNumNodes(TreeNode* root) {
        if (!root) {
            return 0;
        }
        
        return getNumNodes(root->left) + getNumNodes(root->right) + 1;
    }
    
    void getRecord(TreeNode* root, int x, int& left, int& right) {
        if (!root) {
            return;
        }
        
        if (root->val == x) {
            left = getNumNodes(root->left);
            right = getNumNodes(root->right);
            found = true;
            return;
        }
        
        if (!found) {
            getRecord(root->left, x, left, right);
        }
        if (!found) {
            getRecord(root->right, x, left, right);
        }
    }
    
    bool btreeGameWinningMove(TreeNode* root, int n, int x) {
        if (!root) {
            return false;
        }
        int left = 0;
        int right = 0;
        getRecord(root, x, left, right);
        
        int rem = n - left - right - 1;
        return (left > n - left || right > n - right || rem > n - rem);
    }
};
```

#### 1146. Snapshot Array

Implement a SnapshotArray that supports the following interface:

- `SnapshotArray(int length)` initializes an array-like data structure with the given length.  **Initially, each element equals 0**.
- `void set(index, val)` sets the element at the given `index` to be equal to `val`.
- `int snap()` takes a snapshot of the array and returns the `snap_id`: the total number of times we called `snap()` minus `1`.
- `int get(index, snap_id)` returns the value at the given `index`, at the time we took the snapshot with the given `snap_id`

 

**Example 1:**

```
Input: ["SnapshotArray","set","snap","set","get"]
[[3],[0,5],[],[0,6],[0,0]]
Output: [null,null,0,null,5]
Explanation: 
SnapshotArray snapshotArr = new SnapshotArray(3); // set the length to be 3
snapshotArr.set(0,5);  // Set array[0] = 5
snapshotArr.snap();  // Take a snapshot, return snap_id = 0
snapshotArr.set(0,6);
snapshotArr.get(0,0);  // Get the value of array[0] with snap_id = 0, return 5
```

- thought

  Using unordered_set to record the change of set, and snap_id to record snap number. The value changing will contain the snap id and index as key, value is the setting value. 

- code

  ```
  // vector<vector<int>> _snapShots;
  
  class SnapshotArray {
  public:
      unordered_map<unsigned int, int> _snapRec;
      int _snapId = 0; 
      
      SnapshotArray(int length) {
          for (int idx = 0; idx < length; ++idx) {
              _snapRec[idx] = 0;    
          }
      }
      
      void set(int index, int val) {
          _snapRec[_snapId*50000+index] = val;
      }
      
      int snap() {
          ++_snapId;
          return _snapId - 1;
      }
      
      int get(int index, int snap_id) {
          unsigned int tmp_id = snap_id*50000 + index;
          while (tmp_id > 0) {
              if (_snapRec.find(tmp_id) != _snapRec.end()) {
                  return _snapRec[tmp_id];
              }
              tmp_id -= 50000;
          }
          return _snapRec[tmp_id];
      }
  };
  
  /**
   * Your SnapshotArray object will be instantiated and called as such:
   * SnapshotArray* obj = new SnapshotArray(length);
   * obj->set(index,val);
   * int param_2 = obj->snap();
   * int param_3 = obj->get(index,snap_id);
   */
  ```

  

#### 1147. Longest Chunked Palindrome Decomposition

Return the largest possible `k` such that there exists `a_1, a_2, ..., a_k` such that:

- Each `a_i` is a non-empty string;
- Their concatenation `a_1 + a_2 + ... + a_k` is equal to `text`;
- For all `1 <= i <= k`,  `a_i = a_{k+1 - i}`.

 

**Example 1:**

```
Input: text = "ghiabcdefhelloadamhelloabcdefghi"
Output: 7
Explanation: We can split the string on "(ghi)(abcdef)(hello)(adam)(hello)(abcdef)(ghi)".
```

**Example 2:**

```
Input: text = "merchant"
Output: 1
Explanation: We can split the string on "(merchant)".
```

**Example 3:**

```
Input: text = "antaprezatepzapreanta"
Output: 11
Explanation: We can split the string on "(a)(nt)(a)(pre)(za)(tpe)(za)(pre)(a)(nt)(a)".
```

**Example 4:**

```
Input: text = "aaa"
Output: 3
Explanation: We can split the string on "(a)(a)(a)".
```

- thought

  Using the greedy method to store the results.  

- code

  ```
  class Solution {
  public:
      int longestDecomposition(string text) {
          int text_len = text.size();
          int stt_left = 0;
          int stt_right = text_len - 1;
          int num_chunk = 0;
          while (stt_left <= stt_right) {
              for (int idx = stt_left; idx <= stt_right; ++idx) {
                  int len = idx - stt_left + 1;
  
                  if (text.substr(stt_left, len) == text.substr(stt_right - len + 1, len)) {
                      num_chunk += (idx == stt_right)?1:2;
                      stt_left = idx + 1;
                      stt_right = stt_right - len;
                      
                      break;
                  }
              }
          }
          
          return num_chunk;
      }
  };
  ```

- To see the reference video, see this one: [Contest 148](https://www.youtube.com/watch?v=R0Re58WF6Do)
