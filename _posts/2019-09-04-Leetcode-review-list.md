---
layout: post
title: Leetcode review list
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false

---



```c++
// 46. Permutations
// Method 1: permutate the number 
vector<vector<int> > permute(vector<int> &num) {
	    vector<vector<int> > result;
	    
	    permuteRecursive(num, 0, result);
	    return result;
    }
    
    // permute num[begin..end]
    // invariant: num[0..begin-1] have been fixed/permuted
	void permuteRecursive(vector<int> &num, int begin, vector<vector<int> > &result)	{
		if (begin >= num.size()) {
		    // one permutation instance
		    result.push_back(num);
		    return;
		}
		
		for (int i = begin; i < num.size(); i++) {
		    swap(num[begin], num[i]);
		    permuteRecursive(num, begin + 1, result);
		    // reset
		    swap(num[begin], num[i]);
		}
    }

// Method 2: more complext method
void Permutate(const vector<int>& nums, vector<vector<int>>& ans,
              vector<int>& record, vector<int> tmp) {
  if (tmp.size() == nums.size()) {
    ans.push_back(tmp);
    return;
  }
  for (int i = 0; i < nums.size(); ++i) {
    if (!record[i]) {
      tmp.push_back(nums[i]);
      record[i] = true;
      Permutate(nums, ans, record, tmp);
      record[i] = false;
      tmp.pop_back();
    }
  }
}  
  
vector<vector<int>> permute(vector<int>& nums) {
  vector<vector<int>> ans;
  vector<int> record(nums.size() + 1, false);
  vector<int> tmp;
  Permutate(nums, ans, record, tmp);
  return ans;
}
```

