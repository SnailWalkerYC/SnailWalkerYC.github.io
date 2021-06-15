---
layout: post
title: Aurora ai interview preparation
date: 2019-08-01
categories: [technology]
tags: [coding]
comments: false
---



- linked list 

In order, reverse order & cycle output. 

Using this linked list to implement like queue. Including push_back, pop_back, print, back. mutext of this. 

Single list is OK. 

Add the template.

```c++
#include <iostream>
#include <vector>

using namespace std;

template<class Type>
class List {
 public:
  struct Node {
    Node* next = nullptr;
    Type val = 0;
  
    Node(Type tmp_val) {
      val = tmp_val;
      next = nullptr;
    }
    Node() {
      next = nullptr;
    }
  };

  List() {
    head = new Node();  
  }
  
  void AddNode(int val) {
    Node* node = new Node(val);
    node->next = head->next;
    head->next = node;
  }
  
  void PrintOrder() const {
    Node* cur = head->next;
    while (cur) {
      std::cout << cur->val << " -> ";
      cur = cur->next;
    }
    std::cout << "nullptr" << std::endl;
  }
  
  void ReverseOrder(const Node* cur) const {
    if (!cur) {
      std::cout << "nullptr";
      return;
    }
    ReverseOrder(cur->next);
    std::cout << " -> " << cur->val;;
  }
  
  void PrintReverOrder() const {
    ReverseOrder(head->next); 
    std::cout << std::endl;
  }
  
  Node* head = nullptr;
};

template <class Type>
class Queue : public List<Type> {
 public:
  Queue(): List<Type>() {}
  
  using List<Type>::Node;
  
  void PushBack(const Type val) {
    AddNode(val);
  }
  
  std::pair<Type, bool> PopBack() {
    if (!this->head->next) return {-1, false};
    const auto cur = this->head->next;
    this->head->next = cur->next;
    const Type val = cur->val;
    delete cur;
    return {val, true};
  }
  
  std::pair<Type, bool> Back() const {
    if (!this->head->next) return {-1, false};
    return {this->head->next->val, true};
  }
  
  std::pair<Type, bool> Front() const {
    if (!this->head->next) {
      return {-1, false};
    }
    auto cur = this->head;
    while (cur->next) {
      cur = cur->next;
    }
    return {cur->val, true};
  }
};

int main() {
  const vector<int> data({1, 2, 3, 4});
  std::cout << "Create list ......";
  List<int> list;
  for (const auto& num : data) {
    list.AddNode(num);
  }
  
  list.PrintOrder();
  list.PrintReverOrder();
  
  std::cout << "Create queue ......";
  Queue<int> queue;
  for (const auto& num : data) {
    queue.AddNode(num);
  }
  queue.PrintOrder();
  queue.PrintReverOrder();
  queue.PopBack();
  queue.PrintOrder();
  queue.PrintReverOrder();
  
  return 0;
}
```

- vector

vector's template implementation. 

```c++
// Self implementation of
// the Vector Class in C++

#include <bits/stdc++.h>
using namespace std;
template <typename T> class vectorClass
{

	// arr is the integer pointer
	// which stores the address of our vector
	T* arr;

	// capacity is the total storage
	// capacity of the vector
	int capacity;

	// current is the number of elements
	// currently present in the vector
	int current;

public:
	// Default constructor to initialise
	// an initial capacity of 1 element and
	// allocating storage using dynamic allocation
	vectorClass()
	{
		arr = new T[1];
		capacity = 1;
		current = 0;
	}

	// Function to add an element at the last
	void push(T data)
	{

		// if the number of elements is equal to the
		// capacity, that means we don't have space to
		// accommodate more elements. We need to double the
		// capacity
		if (current == capacity) {
			T* temp = new T[2 * capacity];

			// copying old array elements to new array
			for (int i = 0; i < capacity; i++) {
				temp[i] = arr[i];
			}

			// deleting previous array
			delete[] arr;
			capacity *= 2;
			arr = temp;
		}

		// Inserting data
		arr[current] = data;
		current++;
	}

	// function to add element at any index
	void push(int data, int index)
	{

		// if index is equal to capacity then this
		// function is same as push defined above
		if (index == capacity)
			push(data);
		else
			arr[index] = data;
	}

	// function to extract element at any index
	T get(int index)
	{

		// if index is within the range
		if (index < current)
			return arr[index];
	}

	// function to delete last element
	void pop() { current--; }

	// function to get size of the vector
	int size() { return current; }

	// function to get capacity of the vector
	int getcapacity() { return capacity; }

	// function to print array elements
	void print()
	{
		for (int i = 0; i < current; i++) {
			cout << arr[i] << " ";
		}
		cout << endl;
	}
};

// Driver code
int main()
{
	vectorClass<int> v;
	vectorClass<char> v1;
	v.push(10);
	v.push(20);
	v.push(30);
	v.push(40);
	v.push(50);
	v1.push(71);
	v1.push(72);
	v1.push(73);
	v1.push(74);

	cout << "Vector size : " << v.size() << endl;
	cout << "Vector capacity : " << v.getcapacity() << endl;

	cout << "Vector elements : ";
	v.print();

	v.push(100, 1);

	cout << "\nAfter updating 1st index" << endl;

	cout << "Vector elements of type int : " << endl;
	v.print();
	// This was possible because we used templates
	cout << "Vector elements of type char : " << endl;
	v1.print();
	cout << "Element at 1st index of type int: " << v.get(1)
		<< endl;
	cout << "Element at 1st index of type char: "
		<< v1.get(1) << endl;

	v.pop();
	v1.pop();

	cout << "\nAfter deleting last element" << endl;

	cout << "Vector size of type int: " << v.size() << endl;
	cout << "Vector size of type char: " << v1.size()
		<< endl;
	cout << "Vector capacity of type int : "
		<< v.getcapacity() << endl;
	cout << "Vector capacity of type char : "
		<< v1.getcapacity() << endl;

	cout << "Vector elements of type int: ";
	v.print();
	cout << "Vector elements of type char: ";
	v1.print();

	return 0;
}
```

- shared_ptr

Copy, assignment.

```c++
template <class Type>
class SharedPtr {
 public:
  SharedPtr() : count_ref_(new int(0)) {}
  
  SharedPtr(T* ptr) : data_(ptr), count_ref(new int(1)) {}
  
  // copy
  SharedPtr(const SharedPtr& obj) {
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    if (data_ != nullptr) {
      ++(*count_ref_);
    }
  }
  
  SharedPtr& operator=(const SharedPtr& obj) {
    CleanUp();
    
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    if (data_ != nullptr) {
      ++(*count_ref_);
    }
  }
  
  // move
  SharedPtr(SharedPtr&& obj) {
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    obj.data_ = obj.count_ref_ = nullptr;
  }
  
  SharedPtr& operator=(SharedPtr && obj) {
    CleanUp();
    
    data_ = obj.data_;
    count_ref_ = obj.count_ref_;
    obj.data_ = obj.count_ref_ = nullptr;
  }
  
  int GetCount() const {
    return *count_ref_;
  }
  
  T& Get() const {
    return data_;
  }
  
  T* operator->() const {
    return data_;
  }
  
  T& operator*() const {
    return data_;
  }
  
  ~SharedPtr() {
    --(*count_ref_);
    if (!*count_ref_) {
      if (data_ != nullptr) delete data_;
      delete count_ref_;
    }
  }
  
 private:
  int* count_ref_ = nullptr;
  T* data_ = nullptr;
};  

// Example
class Box {
 public:
  int length, width, height;
  Box() : length(0), width(0), height(0) {}
};

int main() {
  SharedPtr<Box> obj;
  cout << obj.GetCount() << endl; // prints 0
  SharedPtr<Box> box1(new Box());
  cout << box1.GetCount() << endl; // prints 1
  SharedPtr<Box> box2(box1); // calls copy constructor
  cout << box1.GetCount() << endl; // prints 2
  cout << box2.GetCount() << endl; // also prints 2
 
  return 0;
}
```

- unique_ptr

```c++
template <class Type>
class UniquePtr {
 public:
  UniquePtr() {}
  
  UniquePtr(T* ptr) : data_(ptr) {}
  
  UniquePtr(const UniquePtr& obj) = delete;
  UniquePtr& operator=(const UniquePtr& obj) = delete;
  
  UniquePtr(UniquePtr&& obj) {
    data_ = obj.data_;
    obj.data_ = nullptr;
  }
  
  void operator=(UniquePtr && obj) {
    CleanUp();
    
    data_ = obj.data_;
    obj.data_ = nullptr;
  }
  
  T* operator->() {
    return data_;
  }
  
  T& operator*() {
    return *data_;
  }
  
  ~UniquePtr() {
    if (data_ != nullptr) {
      delete data_;
      data_ = nullptr;
    }
  }
  
 private:
  T* data_ = nullptr;
};

// Example 
int main() {
  // creates a my_unique_ptr object holding a 'Box' object
  UniquePtr<Box> box1(new Box);
  // creates a my_unique_ptr object holding an array of 'Box' objects
  UniquePtr<Box[]> boxArr(new Box[5]);
  Box b1 = boxArr[0]; // index based access
  return 0;
}
```

- permutation of a sentence

```c++
bool checkInclusion(string s1, string s2) {
  unordered_map<int, int> record;
  for (const auto& c : s1) {
    ++record[c];
  }
  int i = 0; 
  int j = 0;
  int cnt = record.size();
  for (; i < s2.size(); ++i) {
    --record[s2[i]];
    if (!record[s2[i]]) {
      --cnt;
      if (!cnt) return true;
    } else {
      while (record[s2[i]] < 0) {
        ++record[s2[j]];
        if (record[s2[j]] == 1)
          ++cnt;
        ++j;
      }
    }
  }
  return false;
}
```

One is KMP.

```c++
#include <iostream>

using namespace std;

const int N = 100010, M = 1000010;

int n, m;
int ne[N];
char s[M], p[N];

int main()
{
    cin >> n >> p + 1 >> m >> s + 1;

    for (int i = 2, j = 0; i <= n; i ++ )
    {
        while (j && p[i] != p[j + 1]) j = ne[j];
        if (p[i] == p[j + 1]) j ++ ;
        ne[i] = j;
    }

    for (int i = 1, j = 0; i <= m; i ++ )
    {
        while (j && s[i] != p[j + 1]) j = ne[j];
        if (s[i] == p[j + 1]) j ++ ;
        if (j == n)
        {
            printf("%d ", i - n);
            j = ne[j];
        }
    }

    return 0;
}
```

- peak & valley

```c++
int findPeakElement(vector<int>& nums) {
  int stt_idx = 0;
  int fin_idx = nums.size() - 1;
  while (stt_idx + 1 < fin_idx) {
    int mid_idx = stt_idx + (fin_idx - stt_idx) / 2;
    if (nums[mid_idx] < nums[mid_idx+1]) stt_idx = mid_idx;
    else if (nums[mid_idx] < nums[mid_idx-1]) fin_idx = mid_idx;
    else return mid_idx;
  }
  return nums[fin_idx] > nums[stt_idx]?fin_idx:stt_idx;
}
```

- LRU cache

```c++
class LRUCache {
 public:
  unordered_map<int, pair<int, list<int>::iterator>> record_;
  list<int> key_;
  int capacity_ = 0;
  
  LRUCache(int capacity) {
    capacity_ = capacity;
  }
    
  int get(int key) {
    auto it = record_.find(key);
    if (it == record_.end()) return -1;
    const auto value = it->second;
    record_.erase(it);
    key_.erase(value.second);
    key_.push_front(key);
    record_[key] = make_pair(value.first, key_.begin());
    return value.first;
  }
    
  void put(int key, int value) {
    auto it = record_.find(key);
    if (it != record_.end()) {
      const auto [_, it_list] = it->second;
      record_.erase(it);
      key_.erase(it_list);
    }
    if (key_.size() >= capacity_) {
      auto back_it = key_.end();
      --back_it;
      record_.erase(*back_it);
      key_.erase(back_it);
    }
    key_.push_front(key);
    record_[key] = std::make_pair(value, key_.begin());
  }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

- LFU cache

```c++
// visit_cnt -> list
// key -> {cnt, value, list iterator}
class LFUCache {
 public:
  class VALUE{
   public:
    int _value;
    int _cnt;
    list<int>::iterator _listItr;
    VALUE(int v, int c, list<int>::iterator list_itr) {
      _value = v;
      _cnt = c;
      _listItr = list_itr;
    }
  };
    
  int _capacity;
  int _minCnt;
  unordered_map<int, VALUE*> _recKey;
  unordered_map<int, list<int>> _hirarchCache;
    
  LFUCache(int capacity) {
    _capacity = capacity;
    _minCnt = 0;
  }
    
  int get(int key) {
    if (_recKey.find(key) == _recKey.end() || _capacity <= 0) {
      return -1;
    }
        
    auto cur_node = _recKey[key];
    _hirarchCache[cur_node->_cnt].erase(cur_node->_listItr);
    if (_minCnt == cur_node->_cnt && !_hirarchCache[cur_node->_cnt].size()) {
      ++_minCnt;
    }
    cur_node->_cnt++;
    _hirarchCache[cur_node->_cnt].push_front(key);
    _recKey[key] = new VALUE(cur_node->_value, cur_node->_cnt, _hirarchCache[cur_node->_cnt].begin());
        
    return cur_node->_value;
  }
    
  void put(int key, int value) {
    if (_capacity <= 0) {
      return;
    }
    if (_recKey.find(key) == _recKey.end() && _recKey.size() < _capacity) {
      _hirarchCache[1].push_front(key);
      VALUE* new_value = new VALUE(
          value,
          1,
          _hirarchCache[1].begin()
      );
      _recKey.insert(make_pair(key, new_value));
      _minCnt = 1;
      return;
    }
        
    if (_recKey.find(key) != _recKey.end()) {
      auto cur_node = _recKey[key];
      _hirarchCache[cur_node->_cnt].erase(cur_node->_listItr);
      if (_minCnt == cur_node->_cnt && !_hirarchCache[cur_node->_cnt].size()) {
        ++_minCnt;
      }
      cur_node->_cnt++;
      _hirarchCache[cur_node->_cnt].push_front(key);
      _recKey[key] = new VALUE(
                value, 
                cur_node->_cnt,                       
                _hirarchCache[cur_node->_cnt].begin());
      return;
    }
        
    auto cur_layer = _hirarchCache[_minCnt];
    int rm_key = cur_layer.back();
        
    _hirarchCache[_minCnt].pop_back();
    _recKey.erase(rm_key);
    _minCnt = 1;
    _hirarchCache[1].push_front(key);
    _recKey[key] = new VALUE(
               value,
               1,
               _hirarchCache[1].begin());
  }
};
```



