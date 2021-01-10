---
layout: post
title: reject sampling and reservoir sampling and random
date: 2020-06-03
categories: [technology]
tags: [tech]
comments: false
---

 - 说明
 随机数部分，考察的是比如对一个形状进行均匀采样，或者从随机数A生成B。采样其中一种叫做“reject sampling”，直到采到需要的样本才停止，可以计算其期望。
 
  - Reject Sampling
  
```c++
// 478. Generate Random Point in a Circle
// Trick: uniform [0, 1] with rand()/RAND_MAX. radius needs sqrt.
// Why sqrt? 3.3
double radius_ = 0;
double x_center_ = 0.0;
double y_center_ = 0.0;
const double PI = 3.14159265;
    
Solution(double radius, double x_center, double y_center) {
  radius_ = radius;
  x_center_ = x_center;
  y_center_ = y_center;  
}
    
vector<double> randPoint() {
  const double angle = (double) rand() / RAND_MAX * PI * 2 - PI ;
  const double r = sqrt((double)rand()/RAND_MAX) * radius_;
  const double new_x = x_center_ + r*cos(angle); 
  const double new_y = y_center_ + r*sin(angle);
 
  return {new_x, new_y};  
}

// Another method is using the formula: (x - x')^2 + (y - y')^2 <= radius^2 to verify to find the suitable solution.

// 470. Implement Rand10() Using Rand7()
int rand10() {
  int val = 40;  
  while (val >= 40) {
    val = 7 * (rand7() - 1) + rand7() - 1;  
  }  
  return val % 10 + 1;  
}
``` 

Ref:
 - [Why sqrt?](https://www.cs.cornell.edu/courses/cs6630/2015fa/notes/pdf-transform.pdf )
 - [rand7() to rand10()](https://leetcode.com/problems/implement-rand10-using-rand7/discuss/151567/C%2B%2BJavaPython-1.183-Call-of-rand7-Per-rand10)
 - [distribution](https://www.shangmayuan.com/a/0e43475c1b884f10b5a2afe7.html)
 - [Thought](https://blog.csdn.net/u013630349/article/details/47908633)
 - [More generalization](https://leetcode.com/problems/implement-rand10-using-rand7/discuss/150301/Three-line-Java-solution-the-idea-can-be-generalized-to-%22Implement-RandM()-Using-RandN()%22)

