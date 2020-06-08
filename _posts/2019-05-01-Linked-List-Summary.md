---
layout: post
title: Linked List Summary
date: 2019-05-01
categories: [technology]
tags: [book]
comments: false
---



- Reverse: dummy nodes & 4 steps changes

```c++
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

- Remove
```c++
// 83. Remove Duplicates from Sorted List
ListNode* deleteDuplicates(ListNode* head) {
  ListNode* cur = head;    
  while (cur && cur->next) {
    if (cur->val == cur->next->val) {
      ListNode* tmp = cur->next;
      cur->next = cur->next->next;
      delete tmp;
    } else {
      cur = cur->next;
    }
  }    
  return head;
}

// 82. Remove Duplicates from Sorted List II
ListNode* deleteDuplicates(ListNode* head) {
  if (head == nullptr) {
    return head;
  }    
  ListNode* dummy_node = new ListNode(0);
  dummy_node->next = head;
  ListNode* prev = dummy_node;
  ListNode* cur = head;    
  while (cur != nullptr) {
    while (cur->next && cur->val == cur->next->val) {
      cur = cur->next;
    }
    if (prev->next == cur) {
      prev = cur;
    } else {
      prev->next = cur->next;
    }    
    cur = cur->next;
  }    
  return dummy_node->next;
}

// 237. Delete Node in a Linked List
void deleteNode(ListNode* node) {
  const auto next = node->next;
  node->val = next->val;
  node->next = next->next;
  delete next;
}

// 19. Remove Nth Node from the end of the node
ListNode* removeNthFromEnd(ListNode* head, int n) {
  ListNode* dummy_node = new ListNode(0);
  dummy_node->next = head;    
  ListNode* dummy_h1 = dummy_node;
  ListNode* dummy_h2 = dummy_node;
      
  for (int idx = 0; idx <= n; ++idx) {
    dummy_h2 = dummy_h2->next;
  }    
  while (dummy_h2 != nullptr) {
    dummy_h1 = dummy_h1->next;
    dummy_h2 = dummy_h2->next;
  }    
  dummy_h1->next = dummy_h1->next->next;    
  return dummy_node->next;
}
```

- Merge

```C++
// 21. Merge Two Sorted Lists
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
  ListNode* dummy_node = new ListNode(0);
  ListNode* cur = dummy_node;    
  while (l1 && l2) {
    if (l1->val <= l2->val) {
      cur->next = l1;
      l1 = l1->next;
    } else {
      cur->next = l2;
      l2 = l2->next;
    }
    cur = cur->next;
  }    
  cur->next = l1 != nullptr?l1:l2;    
  return dummy_node->next;
}

// 23. Merge k Sorted Lists
class Solution {
public:
  struct Compare {
    bool operator()(ListNode* l, ListNode* r) {
      return l->val > r->val;
    }
  };
  
  ListNode* mergeKLists(vector<ListNode*>& lists) {
    priority_queue<ListNode*, vector<ListNode*>, Compare> cur_heads; 
    for (const auto& l : lists) {
      if (l != nullptr) {
        cur_heads.push(l);
      }
    }  
    ListNode* dummy_node = new ListNode(0);
    ListNode* cur_head = dummy_node;  
    while (!cur_heads.empty()) {
      const auto tp = cur_heads.top();
      cur_heads.pop();
      cur_head->next = tp;
      cur_head = cur_head->next;
      if (tp->next != nullptr) { 
        cur_heads.push(tp->next);
      }
    }   
    return dummy_node->next;
  }
};
```

- Search

```c++
// 876. Middle of the Linked List
ListNode* middleNode(ListNode* head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;    
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    slow_node = slow_node->next;
  }    
  return slow_node;
}

// 141. Linked List Cycle
bool hasCycle(ListNode *head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;    
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    slow_node = slow_node->next;    
    if (fast_node == slow_node) return true;
  }    
  return false;
}

// 142. Linked List Cycle II (2k-k=nr,k=nr, s=k-m, s=nr-m=(n-1)r+(r-m)
ListNode *detectCycle(ListNode *head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;    
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    slow_node = slow_node->next;
    if (slow_node == fast_node) {
      while (head != slow_node) {
        head = head->next;
        slow_node = slow_node->next;
      }
      return head;
    }
  }    
  return nullptr;
}

// 160. Intersection of Two Linked Lists
int GetLinkedListLength(ListNode* head) {
  int len = 0;
  while (head != nullptr) {
    head = head->next;
    ++len;
  }    
  return len;
}

ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
  int len_A = GetLinkedListLength(headA);
  int len_B = GetLinkedListLength(headB);     
  ListNode* longer_node = len_A >= len_B?headA:headB;
  ListNode* shorter_node = longer_node == headA ? headB:headA;    
  for (int i = 0; i < abs(len_A - len_B); ++i) {
    longer_node = longer_node->next;
  }    
  while (longer_node != nullptr) {
    if (longer_node == shorter_node) {
      return longer_node;
    }    
    longer_node = longer_node->next;
    shorter_node = shorter_node->next;
  }    
  return nullptr;
}


```

- Partition

```c++
// 24. Swap Nodes in Pairs
ListNode* swapPairs(ListNode* head) {
  ListNode* dummy_node = new ListNode(0);
  ListNode* cur_node = dummy_node;    
  while (head != nullptr && head->next != nullptr) {
    ListNode* next = head->next;
    ListNode* next_next= next->next;
    cur_node->next = next;
    next->next = head;
    head->next = next_next;
    cur_node = head;
    head = head->next;
  }    
  cur_node->next = head;   
  return dummy_node->next;
}

// 328. Odd Even Linked List
ListNode* oddEvenList(ListNode* head) {
  ListNode* odd_nodes = new ListNode(0);
  ListNode* even_nodes = new ListNode(0);
  ListNode* odd_head = odd_nodes;
  ListNode* even_head = even_nodes;    
  while (head != nullptr && head->next != nullptr) {
    odd_head->next = head;
    odd_head = odd_head->next;
    even_head->next = head->next;
    even_head = even_head->next;
    head = head->next->next;
  }    
  if (head != nullptr) {
    odd_head->next = head;
    odd_head = odd_head->next;
  }   
  even_head->next = nullptr;      
  odd_head->next = even_nodes->next;    
  return odd_nodes->next;
}

// 86. Partition List
ListNode* partition(ListNode* head, int x) {
  ListNode* less_nodes = new ListNode(0);
  ListNode* no_less_nodes = new ListNode(0);
  ListNode* less_head = less_nodes;
  ListNode* no_less_head = no_less_nodes;    
  while (head != nullptr) {
    if (head->val < x) {
      less_head->next = head;
      less_head = less_head->next;
    } else {
      no_less_head->next = head;
      no_less_head = no_less_head->next;
    }
    head = head->next;
  }    
  less_head->next = no_less_nodes->next;
  no_less_head->next = nullptr;    
  return less_nodes->next;
}

// 61. Rotate List
int GetLength(ListNode* head, ListNode*& fin_node) {
  int len = 0;
  while (head != nullptr) {
    fin_node = head;
    head = head->next;
    ++len;
  }
  return len;
}
  
ListNode* rotateRight(ListNode* head, int k) {
  if (head == nullptr) return head;
  ListNode* dummy_h1 = nullptr;
  int len = GetLength(head, dummy_h1);
  dummy_h1->next = head;
  int rem_k = k % len;
  int num = len - rem_k;
  for (int idx = 1; idx < num; ++idx) {
    head = head->next;
  }
  dummy_h1 = head->next;
  head->next = nullptr;
  return dummy_h1;
}

// 234. Palindrome Linked List
ListNode* ReverseLinkedList(ListNode* head) {
  ListNode* cur = head;
  ListNode* prev = nullptr;
  while (cur != nullptr) {
    ListNode* next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }
  return prev;
}
  
bool isPalindrome(ListNode* head) {
  if (head == nullptr) return true;
  ListNode* fast_node = head;
  ListNode* slow_node = head;
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    if (fast_node == nullptr) break;
    slow_node = slow_node->next;
  }    
  ListNode* head2 = ReverseLinkedList(slow_node->next);
  slow_node->next = nullptr;
  while (head != nullptr && head2 != nullptr) {
    if (head->val != head2->val) return false;
      head = head->next;
      head2 = head2->next;
    }  
    return true;
  }
  return false;
}

// 203. Remove Linked List Elements
ListNode* removeElements(ListNode* head, int val) {
  ListNode* dummy_node = new ListNode(0);
  ListNode* dummy_head = dummy_node;
  dummy_node->next = head;
  while (dummy_head->next != nullptr) {
    if (dummy_head->next->val == val) 
      dummy_head->next = dummy_head->next->next;
    else dummy_head = dummy_head->next;
  }    
  return dummy_node->next;
}
```

- Adding

```c++
// 369. Plus One Linked List
ListNode* ReverseLinkedList(ListNode* head) {
  ListNode* prev = nullptr;
  ListNode* cur = head;    
  while (cur != nullptr) {
    const auto next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }    
  return prev;
}
  
void PlusOne(ListNode* head) {
  int cf = 1;    
  while (head != nullptr) {
    const auto new_val = head->val + cf;
    head->val = new_val % 10;
    cf = new_val / 10;
    if (head->next == nullptr) break;
      head = head->next;
  }  
  if (cf && head != nullptr) {
    head->next = new ListNode(cf);
  }
}
  
ListNode* plusOne(ListNode* head) {
  auto reverse_head = ReverseLinkedList(head);
  PlusOne(reverse_head);
  auto new_head = ReverseLinkedList(reverse_head);    
  return new_head;
}

// 445. Add Two Numbers II
ListNode* ReverseLinkedList(ListNode* head) {
  ListNode* cur = head;
  ListNode* prev = nullptr; 
  while (cur != nullptr) {
    const auto next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }
  return prev;
}
  
void PlusTwoListNodes(ListNode* dummy_l1, ListNode* dummy_l2) {
  int cf = 0;
  while (dummy_l1->next != nullptr && dummy_l2->next != nullptr) {
    const int new_val = dummy_l1->next->val + dummy_l2->next->val + cf;
    dummy_l1->next->val = new_val % 10;
    cf = new_val / 10;
    dummy_l1 = dummy_l1->next;
    dummy_l2 = dummy_l2->next;
  }
  if (dummy_l1->next == nullptr) dummy_l1->next = dummy_l2->next;
  while (cf) {
    if (dummy_l1->next == nullptr)  dummy_l1->next = new ListNode(0);
    const int new_val = dummy_l1->next->val + cf;
    dummy_l1->next->val = new_val % 10;
    cf = new_val / 10;
    dummy_l1 = dummy_l1->next;
  }
}
  
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
  auto reverse_l1 = ReverseLinkedList(l1);
  auto reverse_l2 = ReverseLinkedList(l2);
  ListNode* dummy_reverse_l1 = new ListNode(0);
  ListNode* dummy_reverse_l2 = new ListNode(0);
  dummy_reverse_l1->next = reverse_l1;
  dummy_reverse_l2->next = reverse_l2;
  PlusTwoListNodes(dummy_reverse_l1, dummy_reverse_l2);
  return ReverseLinkedList(dummy_reverse_l1->next);
}

// 2. Add Two Numbers
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
  ListNode* dummy_l1 = new ListNode(0);
  ListNode* dummy_l2 = new ListNode(0);
  ListNode* dummy_l1_head = dummy_l1;
  ListNode* dummy_l2_head = dummy_l2;    
  dummy_l1->next = l1;
  dummy_l2->next = l2;
  int cf = 0;
  while (dummy_l1_head->next != nullptr && dummy_l2_head->next != nullptr) {
    const int new_val = dummy_l1_head->next->val + dummy_l2_head->next->val + cf;
    dummy_l1_head->next->val = new_val % 10;
    cf = new_val / 10;
    dummy_l1_head = dummy_l1_head->next;
    dummy_l2_head = dummy_l2_head->next;
  }    
  if (dummy_l1_head->next == nullptr) dummy_l1_head->next = dummy_l2_head->next;    
  while (cf) {
    if (dummy_l1_head->next == nullptr) dummy_l1_head->next = new ListNode(0);
    const int new_val = dummy_l1_head->next->val + cf;
    dummy_l1_head->next->val = new_val % 10;
    cf = new_val / 10;
    dummy_l1_head = dummy_l1_head->next;
  }    
  return dummy_l1->next;
}
```

- Sort

```c++
// 147. Insertion Sort List
ListNode* insertionSortList(ListNode* head) {
  ListNode* dummy_node = new ListNode(0);    
  while (head != nullptr) {
    ListNode* dummy_head = dummy_node;
    ListNode* next = head->next;
    while (dummy_head != nullptr) {
      if (dummy_head->next == nullptr) {
        dummy_head->next = head;
        head->next = nullptr;
        break;
      }
      if (dummy_head->next->val >= head->val) {
        ListNode* dummy_next = dummy_head->next;
        dummy_head->next = head;
        head->next = dummy_next;
        break;
      }
      dummy_head = dummy_head->next;
    }
    head = next;
  }    
  return dummy_node->next;
}

// 143. Reorder List
ListNode* ReverseLinkedList(ListNode* head) {
  ListNode* cur = head;
  ListNode* prev = nullptr;
  while (cur != nullptr) {
    ListNode* next = cur->next;
    cur->next = prev;
    prev = cur;
    cur = next;
  }
  return prev;
}
  
void reorderList(ListNode* head) {
  if (head == nullptr) return;
  ListNode* fast_head = head;
  ListNode* slow_head = head;
  while (fast_head != nullptr && fast_head->next != nullptr) {
    fast_head = fast_head->next->next;
    if (fast_head == nullptr) break;
    slow_head = slow_head->next;
  }
  ListNode* head1 = head;
  ListNode* head2 = slow_head->next;
  slow_head->next = nullptr;
  head2 = ReverseLinkedList(head2);
  while (head2 != nullptr) {
    auto next1 = head1->next;
    head1->next = head2;
    auto next2 = head2->next;
    head2->next = next1;
    head1 = next1;
    head2 = next2;
  }
}

// 148. Sort List
ListNode* FindMiddle(ListNode* head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    if (fast_node == nullptr || fast_node->next == nullptr) break;
    slow_node = slow_node->next;
  }
  return slow_node;
}
  
ListNode* MergeLinkedLists(ListNode* l, ListNode* r) {
  ListNode* dummy_node = new ListNode(0);
  ListNode* dummy_head = dummy_node;
  while (l != nullptr && r != nullptr) {
    if (l->val <= r->val) {
      dummy_head->next = l;
      l = l->next;
    } else {
      dummy_head->next = r;
      r = r->next;
    }
    dummy_head = dummy_head->next;
  }
  dummy_head->next = l == nullptr?r:l;
  return dummy_node->next;
}
  
ListNode* sortList(ListNode* head) {
  if (head == nullptr || head->next == nullptr) return head;
  auto middle = FindMiddle(head);
  ListNode* right_part = middle->next;
  middle->next = nullptr;
  ListNode* left_node = sortList(head);
  ListNode* right_node = sortList(right_part);
  return MergeLinkedLists(left_node, right_node);
}
```

- Structure Change

```c++
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

// 109. Convert Sorted List to Binary Search Tree
// Method 1
ListNode* FindMiddlePrev(ListNode* head) {
  ListNode* fast_node = head;
  ListNode* slow_node = head;
  while (fast_node != nullptr && fast_node->next != nullptr) {
    fast_node = fast_node->next->next;
    if (fast_node == nullptr || fast_node->next == nullptr) break;
    slow_node = slow_node->next;
  }
  return slow_node;
}
  
TreeNode* sortedListToBST(ListNode* head) {
  if (head == nullptr) return nullptr;
  if (head->next == nullptr) return new TreeNode(head->val);
  ListNode* middle_prev = FindMiddlePrev(head);
  ListNode* middle = middle_prev->next;
  middle_prev->next = nullptr;
  TreeNode* left = sortedListToBST(head);
  TreeNode* right = sortedListToBST(middle->next);
  TreeNode* root = new TreeNode(middle->val, left, right);
  return root;
}

// Method 2
ListNode* cur = nullptr;
int GetLength(ListNode* head) {
  int len = 0;
  while (head != nullptr) {
    head = head->next;
    ++len;
  }
  return len;
}
  
TreeNode* SortListToBST(int size) {
  if (size <= 0) return nullptr;
  TreeNode* left = SortListToBST(size/2);
  int cur_val = cur->val;
  cur = cur->next;
  TreeNode* right = SortListToBST(size - 1 - size/2);
  return new TreeNode(cur_val, left, right);
}
  
TreeNode* sortedListToBST(ListNode* head) {
  cur = head;
  int size = GetLength(head);     
  return SortListToBST(size);
}
```