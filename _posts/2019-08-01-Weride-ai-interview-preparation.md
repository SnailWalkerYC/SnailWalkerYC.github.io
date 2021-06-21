---
layout: post
title: Weride ai interview preparation
date: 2019-08-01
categories: [technology]
tags: [coding]
comments: false
---

```c++
// 341. Flatten Nested List Iterator
// method 1
class NestedIterator { 
 public: 
    NestedIterator(vector<NestedInteger> &nestedList) {
      Traverse(nestedList);
    }
    
    int next() {
      return values_[count_++];
    }
    
    bool hasNext() {
      if (count_ < values_.size()) return true;
      else return false;
    }
  
 private:
  void Traverse(const vector<NestedInteger> &nestedList) {
    for (const auto list : nestedList) {
        if (list.isInteger()) {
          values_.emplace_back(list.getInteger());
          cout << list.getInteger() << endl;
        } else {
          Traverse(list.getList());
        }
      }
  }
  
  std::vector<int> values_;
  int count_ = 0;
};

// method 2
// using stack
```



```c++
// 284. Peeking Iterator
class PeekingIterator : public Iterator {
 public:
  bool call_peak = false;
  int val = INT_MIN;
  
	PeekingIterator(const vector<int>& nums) : Iterator(nums) {
	    // Initialize any member here.
	    // **DO NOT** save a copy of nums and manipulate it directly.
	    // You should only use the Iterator interface methods.
	}
	
    // Returns the next element in the iteration without advancing the iterator.
	int peek() {
    if (call_peak) return val;
    val = next();
    call_peak = true;
    return val;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	int next() {
	  if (call_peak) {
      call_peak = false;
      return val;
    }  
    return Iterator::next();
	}
	
	bool hasNext() const {
	  return Iterator::hasNext() || call_peak;  
	}
};
```



```c++
// 75. Sort Colors
// method 1: sort 0 | 1 2, then 1 | 2
void sortColors(vector<int>& nums) {
  if (!nums.size()) return;
  int i = 0;
  int j = nums.size() - 1;
  while (i < j) {
    while (i <= j && !nums[i]) ++i;
    while (i <= j && nums[j]) --j;
    if (i < j) {
      swap(nums[i++], nums[j--]);
    }
  }
  j = max(0, j);
  if (!nums[j]) ++j;
  if (j >= nums.size()) return;
  i = j;
  j = nums.size() - 1;
  while (i < j) {
    while (i <= j && nums[i] == 1) ++i;
    while (i <= j && nums[j] == 2) --j;
    if (i < j) {
      swap(nums[i++], nums[j--]);
    }
  }
}
```

