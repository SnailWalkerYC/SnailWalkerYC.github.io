---
layout: post
title: More Effective C++
date: 2020-03-18
categories: [technology]
tags: [book]
comments: false
---



- tricks

  explicit: 放在class构造函数前面前用于阻止隐式类型转换，比如类B有构造函数explicit B(int a); 有函数void dosth(B b); 加了explicit不能执行dosth(28),但是可以执行dosth(B(28))这样。一般在构造函数加explicit是很好的习惯，不会有超出预期的编译器解释。减少不明确行为的发生，否则debug很累。

- rule 1

  **将C++视为语言联邦，而不是一种语言**。C++支持多重范型编程，面向过程（procedural）；面向对象（object oriented）；面向函数（functional）；泛型编程（generic）；元编程（meta programming）~ 

- rule 2

  **尽量使用const、enum、inline替换#define，**因为预处理器会将宏代替成具体的组成，如果编译器报错就是报替换后的，找到这样的错误很麻烦，尤其用的是别人的文件。想加给某个class的常量，使用static const即可，在cpp里面还需要对这个变量进行声明。enum是一个声明常量的方法。其实使用namespace声明constexpr也是一个很好的方法。

- rule 3

  尽可能用const就加const。const加在指针符号的位置不同就表示不同的意思~ const的object调用const的函数。