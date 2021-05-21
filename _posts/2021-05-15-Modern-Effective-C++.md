---
layout: post
title: Modern Effective C++
date: 2021-05-15
categories: [technology]
tags: [book]
comments: false
---



### Type Inference

> template



> auto



> decltype



> see type



### auto

> auto is better。auto使用了类型推导。并且lambda里面可以使用auto。
>
> auto derefLess = []( const auto& p1, const auto& p2 ) { return *p1 < *p2; };
>
> std::function<bool  (const std::unique_ptr<Widget> &, const std::unique_ptr<Widget>&)> func; std::function比lambda更大更慢。



> auto不符合要求的时候，带有显式型别的初始化方法。比如有时候类别返回的是reference，代理类，其实你需要的是具体的内容，这时候需要转化为具体的。auto sum = static_cast<Matrix>(m1 + m2 + m3 + m4); 这时候不使用auto更加清晰，比如：Matrix sum = m1 + m2 + m3 + m4; 这里就会做隐式转换。



### modern c++

> () and {}。优先选择()而不是{}，{}不适合用来初始化。也是非常让人疑惑的，只有C++这么做。但是{}的好处是会进行阻止隐式类型转化；以及解析语法错误（很少用到）{}有的问题是如果有initilaizer_list的时候为形参的时候。



>  优先使用nullptr，而不是0或NULL。消除NULL的多义型，导致重载的时候调用错函数。nullptr没有整型类型，也没有指针类型。



> 优先使用别名using，而不是typedef。typedef不支持模板化。



> 优先使用enum class，而不是enum。使用tuple的时候，size_t来获取元素，更让代码清晰。



> 优先使用删除函数，delete，而不是private未定义的函数。



> 为改写的函数增加override。写在多态函数的结尾，同类似的还有final。



> 优先使用const_iterator，而不是iterator。



> 只要函数不会发射异常，就为其加上noexcept声明。



> 只要有可能使用constexpr，就尽量使用。




> 保证const成员函数的线程安全性：如果只是单个进行同步的变量，使用std::atomic即可，否则使用互斥量。



> 理解特种成员函数的生成机制。默认构造函数、析构函数、复制构造函数、复制赋值运算符、移动构造函数和移动赋值运算符。成员模板函数不会抑制特种成员函数的生成。



### 智能指针

> unique_ptr：管理专属所有权的资源。可以自定义析构器。使用unique_ptr的reset来实现factory mode。



> shared_ptr：管理共享所有权的资源。裸指针的两倍，指向资源的指针和指向引用计数的指针。



> shared_ptr会悬空的时候，使用weak_ptr。以及shared_ptr的指针环路。



> make_unique, make_shared，消除了异常。



### 右值引用 & 移动语义 & 完美转发

> move & forward: move是强制类型转换，转成右值；forward需要加模板类型，以及需要输入的是右值才会进行强制类型转换为右值。运行时，两者都不会做任何操作。



> T&&类型，如果T是由类型推导或者是auto&&声明类型的时候，该形参或者对象就是个万能引用。类型不具备type&&或类型推导没有发生，就是右值引用。右值来初始化万能引用，那么就是右值引用；如果使用左值来初始化万能引用，就会得到左值引用。



> 







