---
layout: post
title: Robinhood interview preparation
date: 2019-09-04
categories: [technology]
tags: [coding]
comments: false
---



https://juejin.cn/post/6844904085913600008

## Question

**Object Oriented Programming**: to encapsulate the data member & the objects' responding methods into one class. When we would like to use this class, only make the instance of the class into object. There are three key concepts in OOP design. inheritance(a *class* to extend or override functionality of another *class*. The so-called *subclass* has a whole section that is derived (inherited) from the *superclass* and then it has its own set of functions and data.); polymorphism(The ability to replace an *object* with its *subobjects*. The ability of an *object-variable* to contain, not only that *object*, but also all of its *subobjects*. In the implementation, we will use virtual function to implement this dynamic binding. In the object initialization, create the object with its sub class, and in the real running, the object will call its child function. Polymorphism means "many forms", it means to have different functions in different situation, just like functions keys on keyboard, same key but with different functions in different pages. B,C,D inherits from A and have same function but performs differently when is called) & encapsulate(hide details for the user, and only the methods could be exposed to the user.). 

For the inheritance (Is-A) & the composition(Has-A), prefer to use the composition, not the inheritance in the <Design Pattern> book.

[Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection): The basic idea is that if an object depends upon having an instance of some other object then the needed object is "injected" into the dependent object; for example, being passed a database connection as an argument to the [constructor](https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming)) instead of creating one internally.

[Acyclic dependencies principle](https://en.wikipedia.org/wiki/Acyclic_dependencies_principle): The dependency graph of packages or components (the granularity depends on the scope of work for one developer) should have no cycles. This is also referred to as having a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph).[[2\]](https://en.wikipedia.org/wiki/Object-oriented_design#cite_note-objectMentor-2) For example, package C depends on package B, which depends on package A. If package A also depended on package C, then you would have a cycle.

[Composite reuse principle](https://en.wikipedia.org/wiki/Composite_reuse_principle): Favor [polymorphic](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) [composition](https://en.wikipedia.org/wiki/Object_composition) of objects over inheritance. Composition is using polymorphic feature in OOP & define the implementation interface. Has-a relation.

Low coupling & high cohesion: coupling/cohesion -> Cohesion refers to the degree to which the elements of a module/class belong together, it is suggested that the related code should be close to each other, so we should strive for high cohesion and bind all related code together as close as possible. It has to do with the elements **within** the module/class; Coupling refers to the degree to which the different modules/classes depend on each other, it is suggested that all modules should be independent as far as possible, that's why low coupling. It has to do with the elements **among** different modules/classes. High is good -> when parts of a module are grouped because they all contribute to a single well-defined task of the module. **High cohesion**: Elements within one class/module should functionally belong together and do one particular thing. **Loose coupling:** Among different classes/modules should be minimal dependency.

Recursion: **recursion** is a method of solving a problem where the solution depends on solutions to smaller instances of the same problem.

Functional programming: is a [programming paradigm](https://en.wikipedia.org/wiki/Programming_paradigm) where programs are constructed by [applying](https://en.wikipedia.org/wiki/Function_application) and [composing](https://en.wikipedia.org/wiki/Function_composition_(computer_science)) [functions](https://en.wikipedia.org/wiki/Function_(computer_science)). It is a [declarative programming](https://en.wikipedia.org/wiki/Declarative_programming) paradigm in which function definitions are [trees](https://en.wikipedia.org/wiki/Tree_(data_structure)) of [expressions](https://en.wikipedia.org/wiki/Expression_(computer_science)) that map [values](https://en.wikipedia.org/wiki/Value_(computer_science)) to other values, rather than a sequence of [imperative](https://en.wikipedia.org/wiki/Imperative_programming) [statements](https://en.wikipedia.org/wiki/Statement_(computer_science)) which update the [running state](https://en.wikipedia.org/wiki/State_(computer_science)) of the program.

Programming Paradigm: impetative (how to change its status -> OOP; Procedual) declaraticve (declare its desired status, in which the desired result is declared as the value of a series of function applications,)



**Parallel Programming & concurrency/System Issue** CPU -> computation unit. RAM(random access memory) -> Store current running pragram/data. To separate the computation & memory, the frequency between CPU/RAM has large gap. There is memory hirarchy in the memory, disk -> RAM/memory -> L3 cache -> L2 cache -> L1 cache. To speed up the data processing. And the price of the storage is different. Why cache? Locality. Temporal locality/Spatial locality.

Parallel/Concurrency: concurrency(like the multiple threads running at the sam CPU, but use different CPU time. Concurrency means multiple tasks which start, run, and complete in overlapping time periods, in no specific order) Parallelism: when multiple task run at the same time. **Concurrency** is essentially applicable when we talk about minimum two tasks or more. When an application is capable of executing two tasks virtually at same time, we call it concurrent application. Though here tasks run looks like simultaneously, but essentially they MAY not. They take advantage of CPU time-slicing feature of operating system where each task run part of its task and then go to waiting state. When first task is in waiting state, CPU is assigned to second task to complete it’s part of task. **Parallelism** does not require two tasks to exist. It literally physically run parts of tasks OR multiple tasks, at the same time using multi-core infrastructure of CPU, by assigning one core to each task or sub-task.

Multi-Threaded/Multi-Process programming: the multi-core CPU support multple threads running simultaneously. The multi-threads could be created from one process. The cost of process is higher than thread. Multi-threads will share the common address in the process. So there is race condition need to handle in multi-threads. 

Race condition: the condition of an [electronics](https://en.wikipedia.org/wiki/Electronics), [software](https://en.wikipedia.org/wiki/Software), or other [system](https://en.wikipedia.org/wiki/System) where the system's substantive behavior is dependent on the sequence or timing of other uncontrollable events. It becomes a [bug](https://en.wikipedia.org/wiki/Software_bug) when one or more of the possible behaviors is undesirable.

Memory leak: the memory used is not freed or released when this data is no longer needed, and no memory collection for this. Check for the code which variable using the allocated memory, how this is used. If in the modern C++(we can use the smart pointer for the memory collection) or Java already has garbage collection.

Connection pool timeout: check the log and print the abormal time. Using the profiling methods, test the computation frequency of this.

Thread Exhausion: . Typically in a server situation, it depends on the socket settings. Either the socket connection gets queued by the OS or the connection gets refused. This is usually *not* handled by the thread-pool. In ~unix operation systems, this queue or "backlog" is handled by the listen method.

Thread pool implementation: the pool size can be static, and when a new thread is requested the caller will wait on a synchronization primitives like a semaphore, or the request can be pushed into a queue; the pool size can be unlimited but this may be dangerous because creating too much threads can greatly reduce the performance; more often than note it is ranged between a min and a max set by the pool user; the pool can use a dynamic policy depending on the context: hardware resources like CPU or RAM, OS resources like synchronization primitives and threads, current process resources (memory, threads, handles...)

// https://zhuanlan.zhihu.com/p/98793489 无锁队列。

如果有multiple threads access一个queue，该怎么确保poll/add/peek的正确性，我就提了提read/write lock。lock。

Atomic: Atomic operations use low level/hardware level locks to make some operations ATOMIC: operations which are virtually performed in one go/cpu cycle. So atomic operations never place system in inconsistent state.

Mutex: cpu idle time for waiting.

Threads usage too high: The best solution to limiting the **cpu usage** for a process or **thread** is to make sure that the **thread** or process uses less **cpu**. That can best be done by improving the efficiency of the code, or by calling it less often. The aim is to make sure that the process doesn't continually consume all of its available time slice



**Distributed system**: consistent hasing -> balance/monotonicity/spread/load. Map object to the 2^32 loop cycle. Map nodes and objects both into the hash. When node deleted, only the hash value in node2 will be reassigned. When added, only closed objects will be reassigned.

message queue: Message queues can significantly simplify coding of decoupled applications, while improving performance, reliability and scalability. used for [inter-process communication](https://en.wikipedia.org/wiki/Inter-process_communication) (IPC), or for inter-[thread](https://en.wikipedia.org/wiki/Thread_(computing)) communication within the same process. They use a [queue](https://en.wikipedia.org/wiki/Queue_(data_structure)) for [messaging](https://en.wikipedia.org/wiki/Message_(computer_science)) – the passing of control or of content. [Group communication systems](https://en.wikipedia.org/wiki/Group_communication_system) provide similar kinds of functionality.

consistency level: strict consistency(requires events to be ordered in the same real-time sequence in which they occurred) > sequential consistency(order of all operations) -> **Linearizability/ Atomic Consistency** -> casual consistency(***only related operations to have a global ordering between them\***) -> evantually consistency.



Network & security: Web：

HTTP: The **Hypertext Transfer Protocol** (**HTTP**) is an [application layer](https://en.wikipedia.org/wiki/Application_layer) protocol for distributed, collaborative, [hypermedia](https://en.wikipedia.org/wiki/Hypermedia) information systems.[[1\]](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#cite_note-ietf2616-1) HTTP is the foundation of data communication for the [World Wide Web](https://en.wikipedia.org/wiki/World_Wide_Web), where [hypertext](https://en.wikipedia.org/wiki/Hypertext) documents include [hyperlinks](https://en.wikipedia.org/wiki/Hyperlink) to other resources that the user can easily access, for example by a [mouse](https://en.wikipedia.org/wiki/Computer_mouse) click or by tapping the screen in a web browser.

POST vs PUT: **POST** means "create new" as in "Here is the input for creating a user, create it for me". **PUT** means "insert, replace if already exists".

**Cookies** and **Sessions** are used to store information. **Cookies** are only stored on the client-side machine, while **sessions** get stored on the client as well as a server. A **session** creates a file in a temporary directory on the server where registered **session** variables and their values are stored.

RPC: In [distributed computing](https://en.wikipedia.org/wiki/Distributed_computing), a **remote procedure call** (**RPC**) is when a computer program causes a procedure ([subroutine](https://en.wikipedia.org/wiki/Subroutine#:~:text=In_computer_programming,_a_subroutine,particular_task_should_be_performed.)) to execute in a different address space (commonly on another computer on a shared network), which is coded as if it were a normal (local) procedure call, without the programmer explicitly coding the details for the remote interaction. That is, the programmer writes essentially the same code whether the subroutine is local to the executing program, or remote.

有一个API call比较慢怎么debug”这样的short answer（find bottleneck of current API call, the database, the cache, or the data format, resources） 

ip address: An **Internet Protocol address** (**IP address**) is a numerical label assigned to each device connected to a [computer network](https://en.wikipedia.org/wiki/Computer_network) that uses the [Internet Protocol](https://en.wikipedia.org/wiki/Internet_Protocol) for communication.

web: **World Wide Web** (**WWW**), commonly known as the **Web**, is an [information system](https://en.wikipedia.org/wiki/Information_system) where documents and other [web resources](https://en.wikipedia.org/wiki/Web_resource) are identified by [Uniform Resource Locators](https://en.wikipedia.org/wiki/URL) (URLs, such as https://example.com/), which may be interlinked by [hyperlinks](https://en.wikipedia.org/wiki/Hyperlink), and are accessible over the [Internet](https://en.wikipedia.org/wiki/Internet).

network: **Web Networks** is a non-profit organisation based in Toronto, Canada that provides website services to socially committed organizations. It was conceived at a 1986 "Friends of the Earth" conference in Ottawa, Ontario and founded by [Mike Jensen](https://en.wikipedia.org/wiki/Mike_Jensen_(internet_pioneer)) and Kirk Roberts and others in 1987 as project of NIRV Centre and called "The Web".

authorization vs authentication: **Authentication** confirms that users are who they say they are. **Authorization** gives those users permission to access a resource.



**Database:** relational database -> sql (**Structured Query Language**)

relational database： This model organizes data into one or more [tables](https://en.wikipedia.org/wiki/Table_(database)) (or "relations") of [columns](https://en.wikipedia.org/wiki/Column_(database)) and [rows](https://en.wikipedia.org/wiki/Row_(database)), with a unique key identifying each row. Rows are also called [records](https://en.wikipedia.org/wiki/Record_(computer_science)) or [tuples](https://en.wikipedia.org/wiki/Tuple).[[11\]](https://en.wikipedia.org/wiki/Relational_database#cite_note-11) Columns are also called attributes. Generally, each table/relation represents one "entity type" (such as customer or product). The rows represent instances of that type of entity (such as "Lee" or "chair") and the columns representing values attributed to that instance (such as address or price).

ACID: transactional system atomicity([Transactions](https://en.wikipedia.org/wiki/Database_transaction) are often composed of multiple [statements](https://en.wikipedia.org/wiki/SQL_syntax). [Atomicity](https://en.wikipedia.org/wiki/Atomicity_(database_systems)) guarantees that each transaction is treated as a single "unit", which either succeeds completely, or fails completel); consistency([Consistency](https://en.wikipedia.org/wiki/Consistency_(database_systems)) ensures that a transaction can only bring the database from one valid state to another, maintaining database [invariants](https://en.wikipedia.org/wiki/Invariant_(computer_science)): any data written to the database must be valid according to all defined rules, including [constraints](https://en.wikipedia.org/wiki/Integrity_constraints), [cascades](https://en.wikipedia.org/wiki/Cascading_rollback), [triggers](https://en.wikipedia.org/wiki/Database_trigger), and any combination thereof); isolation levels(A lower isolation level increases the ability of many users to access the same data at the same time, but increases the number of [concurrency](https://en.wikipedia.org/wiki/Concurrency_(computer_science)) effects (such as dirty reads or lost updates) users might encounter. Conversely, a higher isolation level reduces the types of concurrency effects that users may encounter, but requires more system resources and increases the chances that one transaction will block another.): durability -> [Durability](https://en.wikipedia.org/wiki/Durability_(computer_science)) guarantees that once a transaction has been committed, it will remain committed even in the case of a system failure 

relational vs key-value: like map; one is select.

Vertical scalability(scale up, improve single machine's memory & CPU, for the databse) vs horizontal scale(more machines, for web)





## Coding

```c++
// https://www.jianshu.com/p/fdbcba5fe5bc
// Longest common continuous subarray
// 718. Maximum Length of Repeated Subarray
vector<string> LongestCommonContinuousHistory(vector<string>& str1,
                                              vector<string>& str2) {
  vector<vector<int>> record(str1.size() + 1, vector<int>(str2.size() + 1, 0)); 
  pair<int, int> pos;  
  int cur_max = 0;
  for (int i = 0; i < str1.size(); ++i) {
    for (int j = 0; j < str2.size(); ++j) {
      if (str1[i] == str2[j]) {
        record[i+1][j+1] = 1 + record[i][j];
        if (cur_max < record[i+1][j+1]) {
          cur_max = record[i+1][j+1];
          pos = make_pair(i, j);
        }
      }
    }
  }
  int len1 = i;
  int len2 = j;
  vector<string> ans;
  while (len1 >= 0 && len2 >= 0 && str1[len1] == str2[len2]) {
    ans.emplace_back(str1[len1]);
    --len1;
    --len2;
  }
  reverse(begin(ans), end(ans));
  return ans;
}

// 1143. Longest Common Subsequence
int longestCommonSubsequence(string text1, string text2) {
  int len1 = text1.size();
  int len2 = text2.size();
  vector<vector<int>> record(len1 + 1, vector<int>(len2 + 1, 0));
  for (int i = 0; i < len1; ++i) {
    for (int j = 0; j < len2; ++j) {
      int increment = text1[i] == text2[j] ? 1:0;
      record[i+1][j+1] = max(record[i][j] + increment, 
                         max(record[i][j+1], record[i+1][j]));
    }
  }
  return record[len1][len2];
}

// 811. Subdomain Visit Count
vector<string> splitString(string cpd, int& num) {
  string cur="";
  for(auto c:cpd) {
    if(isdigit(c)) {
      num = num*10 + c-'0';
    }    
    else 
      break;
  }    
  bool first = true;
  vector<string> ans;
  for(int ind=cpd.size()-1; ind>=0; ind--) {
    if(isalpha(cpd[ind])) 
      cur = cpd[ind]+cur; 
    else if(cpd[ind]=='.') {
      ans.push_back(cur);
      first = false;
      cur = cpd[ind]+cur;
    }    
    else
      break;
  }   
  ans.push_back(cur);
  return ans;
}    
vector<string> subdomainVisits(vector<string>& cpdomains) {
  unordered_map<string, int> rec;
  for(auto cpd:cpdomains) {
    int num=0;
    vector<string>strs = splitString(cpd, num);
    for(auto str:strs)
      rec[str] += num;
    }
    vector<string> ans;
    for(auto ele:rec) {
      ans.push_back(to_string(ele.second)+" "+ele.first);
  }    
  return ans;
}

// log files processing
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;
int string_to_second(const string& str) {
  return (str[1] - '0'+10*(str[0]-'0')) * 3600 + 
     (str[3] - '0'+10*(str[2]-'0')) * 60 + (str[5] - '0'+10*(str[4]-'0'));
}
pair<string, int> most_requested_resource(vector<vector<string>>& logs) {
  unordered_map<string, int> timestamps;    
  for (auto& l : logs) {
    while (l[0].size() < 6) {
      l[0] = "0" + l[0];
    }
    timestamps[l[0]] = string_to_second(l[0]);
  }    
  sort(begin(logs), end(logs), [](
      const vector<string>& v1, const vector<string>& v2) {
    return v1[0] < v2[0];  
  });
  int max_freq = 0;
  string ans;
  unordered_map<string, int> record;
  for (int i = 0, j = 0; i < logs.size(); ++i) {
    ++record[logs[i][2]];
    const auto cur_ts = timestamps[logs[i][0]];
    while (cur_ts - timestamps[logs[j][0]] > 300) {
      ++j;
      --record[logs[j][2]];
    }
    if (record[logs[i][2]] > max_freq) {
      max_freq = record[logs[i][2]];
      ans = logs[i][2];
    }
  }
  return make_pair(ans, max_freq);
}
int main() {
  vector<vector<string>> logs1 = {
    { "58523", "user_1", "resource_1" },
    { "62314", "user_2", "resource_2" },
    { "54001", "user_1", "resource_3" },
    { "200", "user_6", "resource_5" },
    { "215", "user_6", "resource_4" },
    { "54060", "user_2", "resource_3" },
    { "53760", "user_3", "resource_3" },
    { "58522", "user_22", "resource_1" },
    { "53651", "user_5", "resource_3" },
    { "2", "user_6", "resource_1" },
    { "100", "user_6", "resource_6" },
    { "400", "user_7", "resource_2" },
    { "100", "user_8", "resource_6" },
    { "54359", "user_1", "resource_3"},
  };
  vector<vector<string>> logs2 = {
      {"300", "user_1", "resource_3"},
      {"599", "user_1", "resource_3"},
      {"900", "user_1", "resource_3"},
      {"1199", "user_1", "resource_3"},
      {"1200", "user_1", "resource_3"},
      {"1201", "user_1", "resource_3"},
      {"1202", "user_1", "resource_3"},
  };
  const auto res1 = most_requested_resource(logs1);
  cout << "Most frequency resource is " << res1.first << " with " << res1.second << endl;
  const auto res2 = most_requested_resource(logs2);
  cout << "Most frequency resource is " << res2.first << " with " << res2.second << endl;
  return 0;
}
                                            
// https://leetcode.com/problems/backspace-string-compare/
bool backspaceCompare(string S, string T) {
  stack<char> s_stk;
  stack<char> t_stk;
  for (auto c:S) {
    if (c=='#') { 
      if(s_stk.size())
        s_stk.pop();
    }             
    else 
      s_stk.push(c);
  }
  for (auto c:T) {
    if (c=='#') {
      if (t_stk.size()) 
        t_stk.pop();
    }             
    else 
      t_stk.push(c);
  }
  if (s_stk.size()!=t_stk.size())
    return false;
  while (!s_stk.empty()) {
    if (s_stk.top()!= t_stk.top())
      return false;
    s_stk.pop();
    t_stk.pop();
  }
  return true;
}

// https://leetcode.com/problems/compare-strings-by-frequency-of-the-smallest-character/
// 1170. Compare Strings by Frequency of the Smallest Character
int getMinFrequency(string& str) {
  int rec[26] = {0};      
  for (auto c:str) {
    ++rec[c-'a'];
  }
  int min_rec = 1;
  for (int idx = 0; idx < 26; ++idx) {
    if (rec[idx]) {
      return rec[idx];
    }
  }
  return min_rec;
}
void findMinFrequency(vector<string>& words, vector<int>& rec) {
  for (int idx = 0; idx < words.size(); ++idx) {
    rec[idx] = getMinFrequency(words[idx]);
  }
}    
int GetMinPos(int num, vector<int>& vec) {
  if (num < vec[0]) {
    return vec.size();
  }
  if (num >= vec[vec.size()-1]) {
    return 0;
  }      
  int stt_idx = 0;
  int fin_idx = vec.size()-1;
  int mid_idx = stt_idx + (fin_idx-stt_idx)/2;
  while (stt_idx <= fin_idx) {
    mid_idx = stt_idx + (fin_idx-stt_idx)/2;
    if (vec[mid_idx] <= num) {
      ++stt_idx;
    }
    else if(vec[mid_idx] > num) {
      --fin_idx;
    }
  }
  return vec.size() - (mid_idx);
}    
void findPosition(vector<int>& ans, vector<int>& que_vec, 
                  vector<int>& word_vec) {
  for (int idx = 0; idx < que_vec.size(); ++idx) {
    ans[idx] = GetMinPos(que_vec[idx], word_vec);
  }
}    
vector<int> numSmallerByFrequency(vector<string>& queries, vector<string>& words) {
  vector<int> que_vec(queries.size(), 0);
  vector<int> word_vec(words.size(), 0);      
  findMinFrequency(queries, que_vec);
  findMinFrequency(words, word_vec);      
  sort(word_vec.begin(), word_vec.end());      
  vector<int> ans(queries.size(), 0);      
  findPosition(ans, que_vec, word_vec);      
  return ans;
}

// 1. Find rectangle: 给一个2d image array, 里面有0, 1. 0 代表rectangle, 每个rectangle都会有1隔开
// 2. Find shape
function findOneRectangle(board) {
  if (!board || board.length === 0 || board[0].length === 0) {
    return [];
  }
  const result = [];
  for (let i = 0; i < board.length; i++) {
    for (let j = 0; j < board[0].length; j++) {
      if (board[i][j] === 0) {
        result.push([i, j]);
        let height = 1, width = 1;
        while (i + height < board.length && board[i + height][j] === 0) {
          height++;
        }
        while (j + width < board[0].length && board[i][j + width] === 0) {
          width++;
        }
        result.push([i + height - 1, j + width - 1]);
        break;
      }
      if (result.length !== 0) {
        break;
      }
    }
  }
  return result;
}
function findMultipleRectangle(board) {
  if (!board || board.length === 0 || board[0].length === 0) {
    return [];    
  }
  const result = [];
  for (let i = 0; i < board.length; i++) {
    for (let j = 0; j < board[0].length; j++) {
      if (board[i][j] === 0) {
        const rectangle = [[i, j]];
        board[i][j] = 1;
        let height = 1, width = 1;
        while (i + height < board.length && board[i + height][j] === 0) {
          height++;
        }
        while (j + width < board[0].length && board[i][j + width] === 0) {
          width++;
        }
        for (let h = 0; h < height; h++) {
          for (let w = 0; w < width; w++) {
            board[i + h][j + w] = 1;
          }
        }
        rectangle.push([i + height - 1, j + width - 1]);
        result.push(rectangle);
      }
    }
  }
  return result;
}
// Flood fill
function findMultipleShapes(board) {
  if (!board || board.length === 0 || board[0].length === 0) {
    return [];    
  }
  const result = [];
  const floodFillDFS = (x, y, shape) => {
    if (x < 0 || x >= board.length || y < 0 || y >= board[0].length || board[x][y] === 1) {
      return;
    }
    board[x][y] = 1;
    shape.push([x, y]);
    floodFillDFS(x + 1, y, shape);
    floodFillDFS(x - 1, y, shape);
    floodFillDFS(x, y - 1, shape);
    floodFillDFS(x, y + 1, shape);
  };
  for (let i = 0; i < board.length; i++) {
    for (let j = 0; j < board[0].length; j++) {
      if (board[i][j] === 0) {
        shape = [];
        floodFillDFS(i, j, shape);
        result.push(shape);
      }
    }
  }
  return result;
}
```




```c++
// 以往面经原题，一个公司打卡时间记录，输出某人个一小时内超过三次记录，如果某个人有多个结果，输出最多的那个一小时记录 
/* https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=720802&highlight=Robinhood%2Bkarat           
原始问题是给name和timestamp，返回所有在一小时内打卡超过三次的name和对应的timestamp。一个tricky的点是timestamp是24小时制，比如855代表8:55am，1325代表1:35pm，在比较时间的时候不能直接相减，而要先做转化。
follow up是给name, timestamp和action (Enter or Exit)，找出在同一时间里都在房间里超过两次以上的group里人最多的那个group，返回里面的所有人和对应的interval. Scanline to do this. */
给 list of [name, time], time is string format: '1300' //下午一点
return: list of names and the times where their swipe badges within one hour. if there are multiple intervals that satisfy the condition, return any one of them.
name1: time1, time2, time3...
name2: time1, time2, time3, time4, time5...
example:
input: [['James', '1300'], ['Martha', '1600'], ['Martha', '1620'], ['Martha', '1530']]
output: {
'Martha': ['1600', '1620', '1530']
}

We are working on a security system for a badged-access room in our company's building.

We want to find employees who badged into our secured room unusually often. We have an unordered list of names and entry times over a single day. Access times are given as numbers up to four digits in length using 24-hour time, such as "800" or "2250".

Write a function that finds anyone who badged into the room three or more times in a one-hour period, and returns each time that they badged in during that period. (If there are multiple one-hour periods where this was true, just return the first one.)

badge_times = [
  ["Paul",     "1355"],
  ["Jennifer", "1910"],
  ["John",      "830"],
  ["Paul",     "1315"],
  ["John",     "1615"],
  ["John",     "1640"],
  ["John",      "835"],
  ["Paul",     "1405"],
  ["John",      "855"],
  ["John",      "930"],
  ["John",      "915"],
  ["John",      "730"],
  ["John",      "940"],
  ["Jennifer", "1335"],
  ["Jennifer",  "730"],
  ["John",     "1630"],
  ["Jennifer",    "5"]
]

Expected output (in any order)
  John:  830  835  855  915  930
  Paul: 1315 1355 1405  

We are working on a security system for a badged-access room in our company's building.

We want to find employees who badged into our secured room unusually often. We have an unordered list of names and entry times over a single day. Access times are given as numbers up to four digits in length using 24-hour time, such as "800" or "2250".

Write a function that finds anyone who badged into the room three or more times in a one-hour period, and returns each time that they badged in during that period. (If there are multiple one-hour periods where this was true, just return the first one.)

badge_times = [

  ["John",      "730"],
  ["John",      "830"], 830
  ["John",      "835"],
  ["John",      "855"],
  ["John",      "915"],
  ["John",      "930"], 930
  ["John",      "940"],
  ["John",     "1615"],
  ["John",     "1640"],
  ["John",     "1630"],

  ["Paul",     "1355"],
  ["Jennifer", "1910"],
  ["Paul",     "1315"],
  ["Paul",     "1405"],
  ["Jennifer", "1335"],
  ["Jennifer",  "730"],
  ["Jennifer",    "5"]
]

Expected output (in any order)
  John:  830  835  855  915  930
  Paul:  1315 1355 1405

n: length of the badge records array
p: number of people in the input

length of each array   
    worst   length of list = n
    avg     length of list = n/p

worst
    O(n) + O(nlogn) => O(nlogn)

Space
    O(n)

"""
def findFreqBadge(badge_times):
    badges = {}
    ans = {}
    #1 process the badge_times and store in a map
    for badgeTime in badge_times:
        name = badgeTime[0]
        time = int(badgeTime[1])
        if name not in badges:
            badges[name] = []
            badges[name].append(time)
        else:
            badges[name].append(time)
#     print(badges)

    #2 process each entry in the map
    for name in badges:   
        times = sorted(badges[name])
        badgeTimes = [] # updated for traversing within 1hrs

        if len(times) < 3:
            continue

        startTime = times[0]
        endTime = startTime + 100

        for time in times:
            if time > endTime:
                if len(badgeTimes) >= 3:
                    break
                # update startTime and endTime
                startTime = badgeTimes[len(badgeTimes)-1]
                endTime = startTime + 100
                badgeTimes.clear()
                badgeTimes.append(startTime)
                badgeTimes.append(time)
                continue
            badgeTimes.append(time)

        if len(badgeTimes) >= 3:
            ans[name] = badgeTimes
            continue

    return ans
print(findFreqBadge(badge_times))
    
    
// badge access,follow up看 desktop保存的图片
badge第一题（more than 3 times within one hour）先聊了solution，然后开始写代码debug了一会跑了test case聊了time/space complexity。然后badge 第二题（largest group）
// 题目：https://www.1point3acres.com/bbs/thread-720802-1-1.html  

vector<vector<string>> InvalidBadgeRecords(vector<vector<string>>& records) {
  unordered_set<string> enters;
  unordered_set<string> exits;
  unordered_map<string, int> records_map;
  
  for (const auto [name, action] : records) {
    if (action == "enter") {
      if (records_map[name] == 1) {
        enters.insert(name);
      } else {
        records_map[name] = 0;
      }
    } else {
      if (!record_map[name]) {
        exits.insert(name);
      } else {
        records_map[name] = 0;
      }
    }
  }                          
  vector<vector<string>> ans;
  ans.push_back(vector<string>(enters.begin(), enters.end()));     
  ans.push_back(vector<string>(exits.begin(), exits.end()));                
  return ans;            
}   

// same with the logs.

```



```c++
// Course schedule
// Course schedule
// 选课三件套
// 第一题是找overlap，第二题是给一条选课路径return中间的课程，第三题是多条选课路径return所有中间课程 all midpoints of course path
// 大意就是给定一堆course pair
/*
pair[0]是要上pair[1]之前必须完成的prerequisite
跟leetcode course schedule一样
这样你就有一个上课的roadmap
从每个root node(没有prerequiste的课)到每个leaf node（没有后续的课）会有很多种path
把全部的path的第n/2个node找出来（A->B->C, return B) 也就是中点
给一个array，里面是（prerequisite，course）的pair，要求return中间的课。
第一问限定只有一个chain，follow up就是一个graph了。
在30分钟之内很难想到optimal的解，两道都brute force了。第一题写了O(n)，follow up写了一个O(2^n), 其实可以O(n^2)。 */
// 第一题是给<student name, course name> tuples返回每两个学生一起上的课。
// 给一堆<prereq course name, course name> tuples返回这条线中间的course coding
#include <unordered_map>
#include <unordered_set>
#include <string>
#include <vector>
unordered_map<string, vector<string>> CourseOverlap(const vector<pair<string, string>>& courses) {
  unordered_map<string, unordered_set<string>> record;
  for (const auto& course : courses) {
    record[course.first].insert(course.second);
  }
  unordered_map<string, vector<string>> ans;
  for (auto it1 = record.begin(); it1 != record.end(); ++it1) {
    auto it2 = it1;
    ++it2;
    while (it2 != record.end()) {
      for (const auto& it3 : it1->second) {
        if (it2->second.find(it3) != it2->second.end()) {
          ans[it1->first + " " + it2->first].emplace_back(it3);
        }
      }
      ++it2;
    }
  }
  return ans;
}  

// one chain to output the mid
string FindMidCourse(vector<vector<string>>& courses) {
  unordered_map<string, int> records;
  unordered_set<string> courses_set;
  for (const auto& c : courses) {
    ++records[c[1]];
    courses_set.insert(c[0]);
    courses_set.insert(c[1]);
  }
  string head;
  for (const auto& c : courses_set) {
    if (courses_set.find(c) == courses_set.end()) {
      head = c;
      break;
    }
  }
  int len = (courses_set.size() + 1) / 2;
  while (len > 0) {
    head = records[head];
    --len;
  }
  return head;
}

// Multiple-graph for the mid
vector<string> FindMidCourses(vector<vector<string>>& courses) {
  unordered_map<string, int> indegrees;
  unordered_set<string> courses_set;
  unordered_map<string, vector<string>> graph;
  for (const auto& c : courses) {
    ++indegrees[c[1]];
    courses_set.insert(c[0]);
    courses_set.insert(c[1]);
    graph[c[0]].emplace_back(c[1]);
  }
  vector<string> ans;
  queue<string> starts;
  for (const auto& c : courses_set) {
    if (indegrees[c] == 0) {
      starts.push(c);
    }
  }
  while (!starts.empt()) {
    vector<string> tmp;
    DFS(graph, 0, tmp);
  }
  return ans;
}
vector<int> DFS(const string root, unordered_map<string, vector<string>>& graph, int depth, vector<int>& ans) {
  if (graph.find(root) == graph.end()) return {0};
  unordered_set<int> lens;
  for (const auto& c : graph[root]) {
    auto res = DFS(c, graph, depth + 1, ans);
    for (const auto l : res) {
      lens.insert(l + 1);
      if (l == depth) {
        ans.push_back(root);
      }
    }
  }
  return vector<int>(begin(lens), end(lens));
}

```



```c++
// Ads Conversion Rate
// Purchase / Ads_clicks_all。点击了广告之后有购买的。

// reflow字符串
function wordWrap(words, maxLen) {
  if (!words || words.length === 0) {
    return [];
  }
  const result = [];
  let i = 0;
  while (i < words.length) {
    let remain = maxLen;
    let count = 0;
    while (i < words.length) {
      if (remain - words[i].length < 0) {
        break;
      }
      count++;
      remain -= words[i++].length + 1;
    }
    result.push(words.slice(i - count, i).join('-'));
  }
  return result;
}
// 68. Text Justification
string Generate(const int num) {
  if (!num) return "";
  else if (num == 1) return " ";
  const string& space_str = Generate(num/2);
  return space_str + space_str + (num&1?" ": "");
}  
  
vector<string> fullJustify(vector<string>& words, int max_width) {
  vector<string> ans;
  vector<string> cur;
  int cur_len = 0;
  for (const auto& word :words) {
    if (word.size() + cur_len + cur.size() <= max_width) {
      cur.emplace_back(word);
      cur_len += word.size();
    } else {
      string str;
      int rem = cur.size() == 1? 0 : (max_width - cur_len) % (cur.size() - 1);
      int space = cur.size() == 1? max_width - cur_len : (max_width - cur_len) / (cur.size() - 1);
      string space_str = Generate(space);
      string rem_str = Generate(1);
      for (int i = 0, j = 0; i < cur.size() - 1; ++i, ++j) {
        string cur_space = space_str;
        if (j < rem) {
          cur_space += rem_str;
        }
        str += cur[i] + cur_space;
      }
      str += cur.back();
      if (cur.size() == 1)
        str += space_str;
      ans.emplace_back(str);
      cur_len = word.size();
      cur.clear();
      cur.emplace_back(word);
    }
  }
  if (!cur.empty()) {
    string space_str = max_width >= cur_len + cur.size()?Generate(max_width - cur_len - cur.size()):"";
    string str;
    for (const auto& s : cur) {
      str += s + " ";
    }
    str = str.substr(0, max_width);
    str += space_str;
    ans.emplace_back(str);
  }
  return ans;
}

// calculator
// 224. Basic Calculator
pair<string, int> Parse(const string& s) {
  int new_idx = 0;
  stack<int> stk;
  int val = 0;
  int inc_size = 0;
  string res;
  string tmp = s;
  while (new_idx < tmp.size()) {
    if (tmp[new_idx] == '(')
      stk.push(new_idx);
    else if (tmp[new_idx] == ')') {
      const int left = stk.top();
      stk.pop();
      val = calculate(tmp.substr(left+1, new_idx - left - 1));
      const auto new_val = to_string(val);
      tmp = tmp.substr(0, left) + new_val + tmp.substr(new_idx + 1);
      const int tmp_inc_size = new_idx - left + 1 - new_val.size();
      new_idx -= tmp_inc_size;
      inc_size += tmp_inc_size;  
      res += new_val;
    } else {
      res += tmp[new_idx];
    }
    if (stk.empty())
      break;
    ++new_idx;
  }
  return { tmp.substr(0, new_idx+1), new_idx + inc_size};
}  
int calculate(string s) {
  int res = 0;
  char op = '+';
  int tmp = 0;
  bool has_num = false;
  
  for (int i = 0; i < s.size(); ++i) {
    if (s[i] == '(') {
      const auto [str, index] = Parse(s.substr(i));
      tmp += calculate(str);
      i += index;
      has_num = true;
    } else if (s[i] >= '0' && s[i] <= '9') {
      tmp = tmp*10 - '0' + s[i];
      has_num = true;
    } else if (s[i] == ' '){
      continue;
    } else {
      const bool is_con = !has_num?true:false;
      res += op == '-'? -1 * tmp : tmp;
      tmp = 0;
      op = is_con? (s[i] == op?'+':'-') : s[i] == '-'?'-':'+';
      has_num = false;
    }
  }
  res += op == '-'? -1 * tmp : tmp;
  return res;
}
// 227. Calculator ii
int calculate(string s) {
  int s_len = s.size();    
  int cur_res = 0;
  int fin_res = 0;
  char ope = '+';
  int cur_num = 0;   
  for (int idx = 0; idx < s_len; ++idx) {
    if (isdigit(s[idx])) {
      cur_num = cur_num*10 + (s[idx] - '0');
    }
    if (s[idx] == '*' || s[idx] == '/' || s[idx] == '+' 
        || s[idx] == '-' || idx == s_len - 1) {
      switch(ope) {
        case '+': cur_res += cur_num; break;
        case '-': cur_res -= cur_num; break;
        case '*': cur_res *= cur_num; break;
        case '/': cur_res /= cur_num; break;    
      }              
      if (s[idx] == '-' || s[idx] == '+' || s_len - 1 == idx) {
        fin_res += cur_res;
        cur_res = 0;      
      }          
      cur_num = 0;
      ope = s[idx];
    }
  }              
  return fin_res;
}
// 772. Basic Calculator III
class Solution {
public:
    string parserString (string s, int idx, int & new_idx) {
        int num_brance = 1;
        string str = "";
        
        for (int i = idx; i < s.size(); ++i) {
            if (s[i] == '(') {
                ++num_brance;
            }
            else if (s[i] == ')') {
                --num_brance;
            }
            
            if (!num_brance) {
                new_idx = i;
                return str;
            }
            
            str += s[i];
        }
        
        return str;
    }
    
    
    int calculate(string s) {
        int s_len = s.size();
        long long cur_num = 0;
        int cur_res = 0;
        int fin_res = 0;
        char ope = '+';
        
        for (int idx = 0; idx < s_len; ++idx) {
            if (s[idx] == '(') {
                int new_idx = 0;
                string str = parserString(s, idx+1, new_idx);
                cur_num = calculate(str);
                idx = new_idx;
            }
            
            if (isdigit(s[idx])) {
                cur_num = cur_num*10 + (s[idx] - '0');
            }
            
            if (s[idx] == '+' || s[idx] == '-' || s[idx] == '*' ||
                s[idx] == '/' || s_len == idx + 1) {
                switch(ope) {
                    case '+': cur_res += cur_num; break;
                    case '-': cur_res -= cur_num; break;
                    case '*': cur_res *= cur_num; break;
                    case '/': cur_res /= cur_num; break;    
                }
                if (s[idx] == '-' || s[idx] == '+' || s_len - 1 == idx) {
                    fin_res += cur_res;
                    cur_res = 0;
                }
                
                ope = s[idx];
                cur_num = 0;
            }
        }
        
        return fin_res;
    }
};
// 770. Basic Calculator IV


// matrix puzzle
// for each row/column to see if the instruction is valid or not.

// grand parents
// Graph: child -> parents.

// meetings 
// Is there free time. 759. Employee Free Time

// sparse vector
// list

// Find treasure
// Simple BFS.

// https://juejin.cn/post/6844904085913600008
// https://www.jianshu.com/p/fdbcba5fe5bc
```

## System Design

### Question

fault tolerance， distributed system， load balance

1. 在一个server上有一个process同时处理多个video，现在发现同时处理超过10个video就会出错，但不知道具体bug是什么。问在debug期间有哪些workaround。Threadpool; process; tracing for the CPU usage, and print special logs.

2. 全世界一万台自动售货机每天同一固定时间给一个server发状态，问这种设计有哪些问题。

3. 要开发一个手机上的围棋游戏，现在要做一个feature是通过分析当前棋局给玩家提供下一步的建议，这个feature可以通过1）在手机端直接跑算法 2）把棋局发到server端，server端跑算法然后把结果返回给手机，问这两种approach各有什么优劣。手机端 diverty很大，占用手机计算资源，power，卡；server的，cost增加，network is not so good，cause delay & no response。

4. Google doc, 可以允许多个人访问同一个doc。每个server可以处理很多不同的doc，但同一个doc只由一个固定的server处理。也就是说访问同一个doc的所有request都会发到同一个server 处理分配doc的load balancer是round ronbin，问这个设计有什么问题。
   答：round ronbin 两个缺点，第一默认所有server的处理能力相当。如果有的server比较强悍，有的server比较弱鸡，会造成资源浪费。2.round ronbin不考虑number of connection request a server received。比如一个server上的doc有很多人同时访问，那么有新的doc进来的时候最好就不要再发到这个server上，改进方法Least Connection load balancing。

5. 谷歌共享文档可以被多人同时访问编辑，现在有文档存在分布式的100台服务器上，已经有“负载均衡配置”，问当用户量访问上升，performance会受到什么影响？

   增长的用户会带的问题主要是 hashing collision，负载增加，解决方案可以使增加 instance OR  improve load balanceing algorihtm followup，对于新增的instances，不改变之前已经在处理的process的ID，新增的process 均衡地allocate到不同instances上（新增的和旧的instances）Google doc：有一个google doc这样的产品很多人可以一起edit，一个doc只能被一台机器处理，一台机器可以处理多个doc。有一个load balancer，hashing的方法是doc_id % 100，如果usage grow了，会发生什么？ （注意是usage，我看到有的老的帖子说成了user grow）system会变慢，因为doc会增加user会增加，机器的network，cpu各种都负载会增加所以就会变慢。我就说如果我们解决这种情况就要加机器，但是现在的hashing function加机器很难，加机器的话基本上mapping都break了，所以要更好地scale，我们要用consistent hashing。
   interviweer又说ok，下一题。。

6. google doc，一个load balancer 有100 instance，hash function是100求模，每个instance可以handle多个document，每个document必须由同一个instance handle， 问usage growth影不影响performance 并说一下理由 这个我有点没说清楚 基本也是clarify了半天 我说到hash collision的时候他让我解释了一下 我说觉得不影响 但是usage grow太多肯定会变慢 总之这题。如果usage grow了，会发生什么？ （注意是usage，我看到有的老的帖子说成了user grow）
   system会变慢，因为doc会增加user会增加，机器的network，cpu各种都负载会增加所以就会变慢。
   这时候interviewer就说ok，下一题。
   我就说我还能再聊一聊，我就说如果我们解决这种情况就要加机器，但是现在的hashing function加机器很难，加机器的话基本上mapping都break了，所以要更好地scale，我们要用consistent hashing。

7. 就是你deisgn google doc 这个xitong , 然后load balancer把access他们的request发送到100台instance 上，（如何route到这些instance 上的就是通过一个hash function: google_doc_id % 100）每个document exclusively handled by one instance, one instance can hold multiple documents。问你如果usage growth， 对系统会产生影响吗？以及原因。

8. 判该下面三种情况哪种用strong consistency 哪种用 eventual consistency。第一个是media stream的meta data （eventual）
   第二个是web 做user analysis，面试官说可以想象成一个web，显示的是hit counter。（eventual）
   第三个是银行的deposit和withdraw （strong）第一个场景类似处理 Netflix 的 streaming data。用 eventual consistency 因为是处理streaming data需要 low latency！第二个场景是 数据统计要记录每个click， 类似 facebook like触发一下的功能。用  eventual consistency 因为不需要 high latency，第三个场景是 bank transaction。用 strong consistency,因为transaction的 read data must always be up to date

9. strong consistency and eventual consistency model used in distributed computing to achieve high availability。strong consistency；All accesses are seen by all parallel processes Strong Consistency offers up-to-date data but at the cost of high latency. eventual consistency 。 if no new updates are made to a given data item, eventually all accesses to that item will return the last updated value. Eventual consistency offers low latency but may reply to "read requests" with unfresh data since all nodes of the database may not have the updated data

10. strong consistency和eventual consistency 给了三个场景 我其实也没听说过这两个词 但是根据我的理解 eventual consistency有点像atomic？我就按照这个理解给面试官先说了一下我猜的definition 第一个场景是20ms查询一个什么东西的detail info 第二个场景是 数据统计要记录每个click 第三个场景是 bank transaction

11. 还是doc， 如果所有的user sign了doc 就给每个user 发一个notification。但是由于系统的问题只有50%的doc 成功的给所有人发松了notifiction，问如果给那些没有成功发送notification的doc重新发送notification该怎么做。
    现有的条件是 1）database 里存着所有的doc id以及这个doc是否被所有user sign了。2） 有500个server，每个server会存有从该server成功发送notification的doc id。答：1）db里加一列表示是否成功发送过notification 2）500个server各自统计发送成功的doc id，然后对db分别做batch write 3）系统对db做batch get 拿到所有没有发送成功的docId，然后重新发送notification

12. 宕机那个 doc需要all people signed 才能发notification log里有发了notification的docID 没发的有一些全sign了有一些没有 这题我真的不太清楚想考啥 所以我就跟他把流程说了一遍。Naive的方法是 traverse 100 server 并且不断更新 signed people set, 最后统一重发，followup如何提高效率？用MapReduce并发处理，因为每一个server没有依赖关系。

13. 系统设计1：谷歌共享文档可以被多人同时访问编辑，现在有文档存在分布式的100台服务器上，已经有“负载均衡配置”，问当用户量访问上升时（usage grows），performance会受到什么影响？Google doc, 有100个instance, 有一个 load balance, assign instance 的方式是 给订文档 id % num of instance, 当user变多会有什么影响？

14. 系统设计2：给了两个一致性哈希的方式名词，那俩名词lz听都没听过，然后给三种场景，问下面三种分布式场景，应该用那种一致性hash方法，以及为什么？

15. 系统设计3：一个共享文档需要很多个人签名，可能成百上千，公司会给这些需要签名人发邮件，但是分布式服务器宕机俩小时，只有50%发出去了，成功的record存在了100台机器里，问怎么找到失败的重发？(500个server, 发email, 当机, 部分server record了部分成功发出去的email, 请问要怎么找到 fail的email 然后重发?) 所有sign了的document id 会save 到一个database，所有发出去了的email 和document信息 会存到一个log file, 找出所有需要重新send notification的文件

16. 假设100台机器和100个文档，每台机器能且只能作为一个文档的服务器，模仿google doc在线给多人同时编辑，问只有部分文档popular的情况下怎么提高性能。

    google doc, ID % num_instance, usage growth
    answer: change mod to consistent hashing and virtual node for balance

17. strong consistency or eventual consistency
   a, web site like youtube
   answer: eventual consistency
   b, click stream for analysis
   answer: eventual consistency
   c, bank for account management
   answer: strong consistency

18. online e-signature document, multiple people need to sign one document. when all have signed, send email to everyone for notification.
   they found bug that they missed 50% email notifications. they have logs for who signs the doc and sending email.
   The user space and usage is very huge. how to find out all those missing email notification.
   Answer: streaming process. send those logs to kafak and use spark to parse and compare of number of signature and email notification by the same document id.







