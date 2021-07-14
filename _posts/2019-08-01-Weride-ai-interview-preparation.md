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



---------------------------------------

```c++
// DFS，给一个K，K 是总播放分钟，给int[int[]], 每一个一维数组代表一个广告，一维数组中有a,b， a是这个广告的时长(分钟， b是广告的报价，要求K分钟里最大利润，然后播每一个广告是至少播放时长/2， 比如10分钟，至少要播5分钟

// 就是给一堆事件和时间，然后如果最近十秒没出现过这个事件就println要是出现过update时间， 输入是String[], Time[]，一一对应，比如 food- 01 shower- 04, food-11, run-15, 还有一个要求是每次遇到新的事件，要把已存下的事件中超过十秒(时间差大于10)的删除, 比如我遇到run-15，就要把shower-4删掉。  我是拿MAP存的，可是后面删除旧事件(超过十秒）会把时间复杂度提高了，面试官说用其他数据结构可以比n平方少

// 239. Sliding Window Maximum -> max difference

// public static void main(String[] args) {
      System.out.println("You're running Java!");
      List<Integer> seq = Arrays.asList(new Integer[]{2, 1, 3, 2, 4, 3, 2, 5, 4, 5});

       // Should print [1, 2, 3, 4, 5]
      System.out.println(findLongestSequence(seq));

      seq = Arrays.asList(new Integer[]{2, 1, 3, 2, 4, 3, 2, 5, 5});
      /*
    * 2 1 3 2 4 3 2 5 5
    * ^   ^    ^     ^
        */
       // Should print [2, 3, 4, 5]
      System.out.println(findLongestSequence(seq));
    }

```

