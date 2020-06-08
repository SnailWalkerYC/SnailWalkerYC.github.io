---
layout: post
title: Linked List Summary
date: 2019-05-01
categories: [technology]
tags: [book]
comments: false
---



- Reverse: dummy nodes & 4 steps changes

```
// 206. Reverse Linked List
ListNode* reverseList(ListNode* head) {
  ListNode* prev = nullptr;
  ListNode* cur = head;    
  while (cur != nullptr) {
    ListNode* next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }
  return prev;
}

// 92. Reverse Linked List II
ListNode* reverseBetween(ListNode* head, int m, int n) {
  ListNode* dummy_node = new ListNode(0);
  dummy_node->next = head;    
  head = dummy_node;
  for (int i = 1; i < m; ++i) {
    head = head->next;
  }  
  ListNode* pose_n = head->next;
  ListNode* cur = pose_n->next;
  ListNode* prev = pose_n;    
  for (int i = m; i < n; ++i) {
    ListNode* next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }    
  head->next = prev;
  pose_n->next = cur; 
  return dummy_node->next;
}
```

- 
- Structure Change
```
// 426. Convert Binary Search Tree to Sorted Doubly Linked List

class Solution {
public:
    Node* prev = nullptr;
  
    void TreeToDoublyList(Node* root) {
      if (root == nullptr) return;
      TreeToDoublyList(root->left);
      root->left = prev;
      if (prev != nullptr) prev->right = root;
      prev = root;
      TreeToDoublyList(root->right);
    }
  
    Node* treeToDoublyList(Node* root) {
      TreeToDoublyList(root);
      Node* left = root;
      Node* right = root;
      while (left != nullptr && left->left != nullptr) {
        left = left->left;
      }
      while (right != nullptr &&  right->right != nullptr) {
        right = right->right;
      }
      if (left != nullptr) {
        left->left = right;
        right->right = left;
      }
      return left == nullptr?root:left;
    }
};
```