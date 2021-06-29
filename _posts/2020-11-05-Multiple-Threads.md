---
layout: post
title: Multiple Threads
date: 2020-11-05
categories: [technology]
tags: [book]
comments: false
---



- history 

C++ 11 introduce thread lib. C++ 17 supports STL in parallel computing. Concurrency programming involves a shared memory localization. Parallelism run simutaneous with different data.

- terminology

Thread, critical section, mutex(block other threads to use the same resource until mutex release), semaphore(used for the signaling among threads，mutex's implementation)

- why concurrency 

minimize data sharing of the mutable data(data race); minimize waiting; deadlock be careful.

- deadlocks

avoid nested locks & avoid unnecessary locks.

- C++ multi-threading lib

```c++
// atomic
// atomic flag
// No race condition.

// thread
// std::thread first(func, "param");
// first.join();
// detach: detach the thread by object from the calling thread, allow exucuting independently.
// joinable: if the thread is not joinable.
// =  "move assignment of the thread."

// mutex: unlock, lock, 
// once_flag, call_once: 
// std::once_flag winnder_flag;
// std::call_once(winnder_flag, set_winner, id);
// unique<std::mutex> lck(mtx);

// condition_variable:
// wait, wait_for, wait_until, notify_once, notify_all.
std::mutex mtx;
std::condition_variable cv;
bool ready = false;
void print_id (int id) {
  std::unique_lock<std::mutex> lck(mtx);
  while (!ready) cv.wait(lck);
  // ...
  std::cout << "thread " << id << '\n';
}
void go() {
  std::unique_lock<std::mutex> lck(mtx);
  ready = true;
  cv.notify_all();
}
int main ()
{
  std::thread threads[10];
  // spawn 10 threads:
  for (int i=0; i<10; ++i)
    threads[i] = std::thread(print_id,i);
  std::cout << "10 threads ready to race...\n";
  go();                       // go!
  for (auto& th : threads) th.join();
  return 0;
}

// future.
#include <iostream>       // std::cout
#include <future>         // std::async, std::future
#include <chrono>         // std::chrono::milliseconds
// a non-optimized way of checking for prime numbers:
bool is_prime (int x) {
  for (int i=2; i<x; ++i) if (x%i==0) return false;
  return true;
}
int main () {
  // call function asynchronously:
  std::future<bool> fut = std::async (is_prime,444444443); 
  // do something while waiting for function to set future:
  std::cout << "checking, please wait";
  std::chrono::milliseconds span (100);
  while (fut.wait_for(span)==std::future_status::timeout)
    std::cout << '.' << std::flush;
  bool x = fut.get();     // retrieve return value
  std::cout << "\n444444443 " << (x?"is":"is not") << " prime.\n";
  return 0;
}
// valid: the function is valid or not.
// get: get the future value.
// wait, wait_for(ready, timeout, deferred), wait_until: 
```



### Example

```c++
// Accumulate Array

```



```c++
// max pooling; convolution; average pooling.
```



### Summary

```c++
// Produce-consumer
void task_queue_t::produce(const task_t& task_) {       
  lock_guard_t lock(m_mutex);
  if (m_tasklist->empty()){//! 条件满足唤醒等待线程
    m_cond.signal();
  }
  m_tasklist->push_back(task_);
}
int task_queue_t::comsume(task_t& task_){
  lock_guard_t lock(m_mutex);
  while (m_tasklist->empty()) {//! 当没有作业时，就等待直到条件满足被唤醒{
    if (false == m_flag){
      return -1;
    }
    m_cond.wait();
  }
  task_ = m_tasklist->front();
  m_tasklist->pop_front();
  return 0;
}

// IO & logic separation
void handle_xx_msg(long uid, const xx_msg_t& msg){
    logic_task_queue->post(boost::bind(&servie_t::proces, uid, msg));
}

// Pipeline
void handle_xx_msg(long uid, const xx_msg_t& msg) {
　　logic_task_queue_array[uid % sizeof(logic_task_queue_array)]->post(
　　　　boost::bind(&servie_t::proces, uid, msg));
}

// Map/Reducer with shared_ptr.
struct reducer{
    void set_result(int index, long result) {
        m_result[index] = result;
    }
    ~reducer(){
        long total = 0;
        for (int i = 0; i < sizeof(m_result); ++i){
            total += m_result[i];
        }
        //! post total to somewhere
    }
    long m_result[10];
};
```

[Multi-threads summary](https://cloud.tencent.com/developer/article/1056593)



**Ref:**

- [Modern C++ concurrency](https://www.educative.io/courses/modern-cpp-concurrency-in-practice-get-the-most-out-of-any-machine)
- C++ Concurrency in Action
- [C++ multi-threading lib](http://www.cplusplus.com/reference/multithreading/) 
- [C++ Multithreading Concurrency](https://www.educative.io/blog/modern-multithreading-and-concurrency-in-cpp)