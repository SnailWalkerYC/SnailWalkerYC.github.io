---
layout: post
title: Multi-threading summary
date: 2019-08-03
categories: [technology]
tags: [coding]
comments: false
---



CMU course: https://www.cs.cmu.edu/~15210/pasl.html

CMU courses: http://www.cs.cmu.edu/~418/

MIT paralle computing: https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-189-multicore-programming-primer-january-iap-2007/lecture-notes-and-video/l6-design-patterns-for-parallel-programming-i/

Stanford courses: http://cs149.stanford.edu/fall20/lecture/

UCB courses: https://sites.google.com/lbl.gov/cs194-15-fa2020

https://sites.google.com/lbl.gov/cs267-spr2020

### memory sharing

- Find the race condition.  Initialization; 

- Using mutex & lock to protect data.
- dead lock: avoid nested lock; 



### concurrency sync operation

```c++
// wait event
// conditional variable.

// wait future
// get result from std::async
std::future<void> res = task.get_future();

// time limitation waiting
// wait_until.

// operation sync
```



### parallel the STL

```c++
std::vector<int> my_data;
std::sort(std::execution::par,my_data.begin(),my_data.end());

// policy
// seq/par/par_unq
```



### example

```c++
template<typename Iterator,typename T>
T parallel_accumulate(Iterator first,Iterator last,T init) {
  unsigned long const length=std::distance(first,last);
  if(!length) return init;
  unsigned long const block_size=25;
  unsigned long const num_blocks=(length+block_size-1)/block_size;
  std::vector<std::future<T> > futures(num_blocks-1);
  thread_pool pool;
  Iterator block_start=first;
  for(unsigned long i=0;i<(num_blocks-1);++i) {
    Iterator block_end=block_start;
    std::advance(block_end,block_size);
    futures[i]=pool.submit([=]{
      accumulate_block<Iterator,T>()(block_start,block_end);
    });
    block_start=block_end;
  }
  T last_result=accumulate_block<Iterator,T>()(block_start,last);
  T result=init;
  for(unsigned long i=0;i<(num_blocks-1);++i) {
    result+=futures[i].get();
  }
  result += last_result;
  return result;
}
```



```c++
// future usage
void test_concurrent_push_and_pop_on_empty_queue()
{
    threadsafe_queue<int> q;
    std::promise<void> go,push_ready,pop_ready;
    std::shared_future<void> ready(go.get_future());
    std::future<void> push_done;
    std::future<int> pop_done;
    try
    {
        push_done=std::async(std::launch::async,
                             [&q,ready,&push_ready]()
                             {
                                 push_ready.set_value();
                                 ready.wait();
                                 q.push(42);
                             }
            );
        pop_done=std::async(std::launch::async,
                            [&q,ready,&pop_ready]()
                            {
                                pop_ready.set_value();
                                ready.wait();
                                return q.pop();
                            }
            );
        push_ready.get_future().wait();
        pop_ready.get_future().wait();
        go.set_value();
        push_done.get();
        assert(pop_done.get()==42);
        assert(q.empty());
    }
    catch(...)
    {
        go.set_value();
        throw;
    }
}
```





Ref:

> https://livebook.manning.com/book/c-plus-plus-concurrency-in-action-second-edition/chapter-3/v-7/43