---
layout: post
title: Modern Effective C++ note
date: 2021-05-15
categories: [technology]
tags: [book]
comments: false
---



### Type Inference

> template：模板推导的时候，参数是指针或者引用，但不是万能引用。
>
> template<typename T>
>
> void f(T& param);
>
> int x = 27;
>
> const int cx = x;
>
> const int& rc = x;
>
> f(rc) -> 引用会被去掉，会变成const int。所以指针和引用下，引用或者指针会被去掉。
>
> param是万能引用，
>
> f(T&& param);
>
> 会根据是左值还是右值进行推导。
>
> f(T param);
>
> 按值的话，都会变成值。
>
> 另外考虑数组或者函数类型的实参退化。



> auto：和上面一样，除了auto可以使用大括号初始化一个std::initializer_list。



> decltype：就是获取改名字的declread type。
>
> template<typename Container, typename Index>
>
> auto authAndAccess(Container& c, Index i) -> decltype(c[i]) {
>
>   autheenticateUser();
>
>    return c[i];
>
> }
>
> Widget w;
>
> const Widget& cw = w;
>
> auto myWidget1 = cw;  // Widget
>
> decltype(auto) myWidgte2 = cw;  // const Widget&



> see type：使用typeid或者std::type_info看类型。



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

> move & forward: move是无条件强制类型转换，转成右值；forward需要加模板类型，以及需要输入的是右值才会进行强制类型转换为右值。运行时，两者都不会做任何操作。forward可以将传进来的右值，转为右值。实参使用右值完成初始化的时候，才会执行向右值型别的强制转换。
>
> forward的一个case，
>
> void logAndProcess(T&& param) {
>
>   process(std::forward<T>(param));
>
> }



> T&&类型，如果T是由类型推导或者是auto&&声明类型的时候，该形参或者对象就是个万能引用。类型不具备type&&或类型推导没有发生，就是右值引用。右值来初始化万能引用，那么就是右值引用；如果使用左值来初始化万能引用，就会得到左值引用。



> 对于右值引用最后一次实施std::move，对于万能引用最后一次实施forward。局部对象的返回值优化，不要使用：move或者forward。



```
// 避免万能引用型别进行重载
// 把万能引用做重载，导致意外调用。完美转发的构造函数问题，尤为严重。
```



> 不使用万能引用和重载的组合，课使用const &T,传值，标签分派。使用enable_if，http://www.cplusplus.com/reference/type_traits/enable_if/
>
> template<typename T, 
>
> ​          typename = typename std::enable_if<
>
>    ! std::is_base_of <Person, typename std::decay<T> >::type>::valu>>::type > 
>
> explicit Person(T&&  n);



> 为什么使用forward，其中一个原因就是引用折叠。如果输入的是左值，虽然有&&，但是还是会被推导成右值。所以必须使用forward，强制转化为右值。引用折叠，会在模板实例化，auto型别生成，创建和使用typedef，using，decltype的时候使用。



> 有些情况下，array，string，移动并没有比拷贝快。可以假定这些类型是不存在，高成本，未使用。对于已知的情况，无需做上面的假设。



> 完美转发指的是可以转发对象，显著特征，左值或者是右值，以及是否有const，volation等等修饰。万能引用才会对左右值进行编码。完美转发，值的是源于模板型别推导失败，或者是推导结果错误的型别。完美转发失败的例子有：大括号初始化，以0或者NULL表达的空指针，仅有声明的整型static const成员变量，模板或者重载的函数名字，以及位域。



### Lambda

> lambda：可以有闭包。auto c1 = [ x ] (int y) { return x * y > 55; } 为了避免lambda的悬空，可以使用拷贝到本地。this指针会在class里面在=的时候被传送。按引用的默认捕获会导致空悬指针的问题；按值默认捕获会受到空悬指针影响（尤其是this）。
>
> void Widget::addFilter()  const {
>
>   auto divisorCopy = divisor;
>
>   filters.emplace_back(
>
> ​    [divisor = divisor] (int value) {
>
> ​      return value % divisor == 0;
>
> ​    }
>
>   );
>
> }



> 使用初始化捕获将对象移入闭包。比如：
>
> auto func = [pw = std::make_unique<Widget>( )] {
>
>   return pw->isValidated() && pw->isArchived();
>
> }



> lambda里面可以使用auto。使用auto&&型别的形参进行decltype，以std::forward。
>
> auto f = [] (auto&& param) {
>
>   return func(normalized(std::forward<decltype(param)>(param)));
>
> }
>
> 在C++14里面，可以使用变长参数
>
> auto f = [] (auto&& ... param) {
>
>   return func(normalized(std::forward<decltype(param)>(param) ...));
>
> }



> lambda比bind可读性更好，表达力更强，可能运行效率更高。



### 并发API

> 优先使用基于任务，而不是线程的设计。
>
> 基于任务：  auto fut = std::async(doAsyncWork);
>
> 基于线程：std::thread t(doAsyncWork);
>
> 基于任务可以获取状态，不会被terminate。同时可以不需要进行人为的线程管理。自己做调度，很容易发生负载不均衡。



> 如果异步是必须的，就是用launch::async，因为async不一定可以实现目标。async默认既允许同步，也允许异步。thread_pool的变量不确定性，使任务永远不会被执行。



> std::thread： join可能导致调试的性能异常；析构调用detach可能导致难以调试的未定义行为。成员列表最后声明std::thread型别对象。型别对象所在的路径皆不可连接。



> 期待的析构函数在常规情况下，仅会析构期待值的成员变量。由async启动的任务是会保持阻塞的，直至任务结束。



> 一次性事件，一般使用pingbang原则进行。
>
> 比如：
>
> std::condition_variable cv;
>
> std::mutex m;
>
> bool flag(false);
>
> {
>
>   std::lock_guard<std::mutex > g(m);
>
>   flag = true;
>
> }
>
> cv.notify_one(); // notify_all
>
> {
>
>   std::unique_ptr<std::mutex > lk(m);
>
>   cv.wair(lk, [] { return flag; } );
>
> }



> atomic，特别适合单一资源的使用。std::atomic<int > ai(0);
>
> ai = 10;
>
> std::cout << ai << std::endl;
>
> ++ai;
>
> --ai;
>
> 如果只有一个线程修改ai，其它线程看到ai的值只能是0，10，11。但是volatile可能是任何值。
>
> register = x.load();
>
> std::atomic<int > y(register);
>
> y.store(register);



### 微调(fine-tune)

> 针对可复制的形参，在移动成本低并且一定会被复制的前提下，考虑按值传递。
>
> 三种方法：
>
> 法一：
>
> void addName(const std::string& newName) {
>
>   names.push_back(newName);
>
> } 
>
> void addName(const std::string&& newName) {
>
>   names.push_back(std::move(newName));
>
> }
>
> 法二：
>
> template<typename T>
>
> void addName(T&& newName) {
>
>   names.push_back(std::forward<T >(newName));
>
> }
>
> 法三：
>
> void addName(std::string newName) {
>
>   names.push_back(std::move(newName));
>
> }



> 考虑置入而不是插入：插入多了构造和析构的环节。使用emplace_back，使用了完美转发，参数出现在vector的构造函数中了。



