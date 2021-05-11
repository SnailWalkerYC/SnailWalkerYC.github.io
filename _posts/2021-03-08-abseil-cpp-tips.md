---
layout: post
title: abseil cpp tips
date: 2021-03-08
categories: [technology]
tags: [tech]
comments: false

---



[TOC]

https://abseil.io/tips/

### string_view

```c++
// Shallow copy from the following implicit
void AlreadyHasString(const std::string& s) {
  TakesStringView(s); // no explicit conversion; convenient!
}

void AlreadyHasCharStar(const char* s) {
  TakesStringView(s); // no copy; efficient!
}

// char* (implicit transform), const string& (temporary copy).

// When using the constant string data. std::string(my_string_view) is not efficient. And adding string_view to an exisiting codebase is not always right answer.

// Not safe to print, as it's not NULL terminated('\0'). Using absl PrintF. But std::cout << "Took '" << s << "'"; is fine.

// https://segmentfault.com/a/1190000018387368
```



### operator + vs StrCat()

```c++
std::string foo = LongString1();
std::string bar = LongString2();
std::string baz = LongString3();
string foobar = foo + bar + baz; // -> std::string temp = foo + bar; std::string foobar = std::move(temp) + baz;

std::string foo = LongString1();
std::string bar = LongString2();
std::string baz = LongString3();
std::string foobar = absl::StrCat(foo, bar, baz);

// More optimization type
absl::StrAppend(&foobar, foo, bar, baz);

// absl::StrCat() and absl::StrAppend() operate on types other than just string types: you can use absl::StrCat/absl::StrAppend to convert int32_t, uint32_t, int64_t, uint64_t, float, double, const char*, and string_view, like this:
std::string foo = absl::StrCat("The year is ", year);
```



### disappearing act

```c++
std::string s1, s2;
const char* p1 = (s1 + s2).c_str();             // Avoid!
const char* p2 = absl::StrCat(s1, s2).c_str();  // Avoid!
// char* becomes danling pointer.

// 1. use full before the end of the full expression
size_t len1 = strlen((s1 + s2).c_str());
size_t len2 = strlen(absl::StrCat(s1, s2).c_str());

// 2. store temporary object
std::string tmp_1 = s1 + s2;
std::string tmp_2 = absl::StrCat(s1, s2);
// tmp_1.c_str() and tmp_2.c_str() are safe.

// 3. store a reference
// Equally safe:
const std::string& tmp_1 = s1 + s2;
const std::string& tmp_2 = absl::StrCat(s1, s2);
// tmp_1.c_str() and tmp_2.c_str() are safe.
// The following behavior is dangerously subtle:
// If the compiler can see you’re storing a reference to a
// temporary object’s internals, it will keep the whole
// temporary object alive.
// struct Person { string name; ... }
// GeneratePerson() returns an object; GeneratePerson().name
// is clearly a sub-object:
const std::string& person_name = GeneratePerson().name; // safe
// If the compiler can’t tell, you’re at risk.
// class DiceSeries_DiceRoll { `const string&` nickname() ... }
// GenerateDiceRoll() returns an object; the compiler can’t tell
// if GenerateDiceRoll().nickname() is a subobject.
// The following may store a dangling reference:
const std::string& nickname = GenerateDiceRoll().nickname(); // BAD!
```



### split string

```c++
// Splits on commas. Stores in vector of string_view (no copies).
std::vector<absl::string_view> v = absl::StrSplit("a,b,c", ',');

// Splits on commas. Stores in vector of string (data copied once).
std::vector<std::string> v = absl::StrSplit("a,b,c", ',');

// Splits on literal string "=>" (not either of "=" or ">")
std::vector<absl::string_view> v = absl::StrSplit("a=>b=>c", "=>");

// Splits on any of the given characters (',' or ';')
using absl::ByAnyChar;
std::vector<std::string> v = absl::StrSplit("a,b;c", ByAnyChar(",;"));

// Stores in various containers (also works w/ absl::string_view)
std::set<std::string> s = absl::StrSplit("a,b,c", ',');
std::multiset<std::string> s = absl::StrSplit("a,b,c", ',');
std::list<std::string> li = absl::StrSplit("a,b,c", ',');

// Equiv. to the mythical SplitStringViewToDequeOfStringAllowEmpty()
std::deque<std::string> d = absl::StrSplit("a,b,c", ',');

// Yields "a"->"1", "b"->"2", "c"->"3"
std::map<std::string, std::string> m = absl::StrSplit("a,1,b,2,c,3", ',');

// More examples: https://abseil.io/tips/10
```



### return policy

```c++
static SomeBigObject SomeBigObjectFactory(...) {
  SomeBigObject local;
  ...
  return local;
}

SomeBigObject obj = SomeBigObject::SomeBigObjectFactory(...);
// two objects created.

// No message about expensive operations:
SomeBigObject obj = SomeBigObject::SomeBigObjectFactory(...);

// RVO won’t happen here; prints message "Expensive assignment ...":
obj = SomeBigObject::SomeBigObjectFactory(s2);

// RVO won’t happen here:
static SomeBigObject NonRvoFactory(...) {
  SomeBigObject object1, object2;
  object1.DoSomethingWith(...);
  object2.DoSomethingWith(...);
  if (flag) {
    return object1;
  } else {
    return object2;
  }
}

// RVO will happen here:
SomeBigObject local;
if (...) {
  local.DoSomethingWith(...);
  return local;
} else {
  local.DoSomethingWith(...);
  return local;
}

// RVO works here:
SomeBigObject SomeBigObject::ReturnsTempFactory(...) {
  return SomeBigObject::SomeBigObjectFactory(...);
}

// No message about expensive operations:
EXPECT_EQ(SomeBigObject::SomeBigObjectFactory(...).Name(), s);
```



### copies abbrv

```c++
// One Name, No Copy; Two Names, Two Copies. RVO.
std::string build();

std::string foo(std::string arg) {
  return arg;  // no copying here, only one name for the data “arg”.
}

void bar() {
  std::string local = build();  // only 1 instance -- only 1 name

  // no copying, a reference won’t incur a copy
  std::string& local_ref = local;

  // one copy operation, there are now two named collections of data.
  std::string second = foo(local);
}
```



### join api

```c++
std::vector<std::string> v = {"a", "b", "c"};
std::string s = absl::StrJoin(v, "-");
// s == "a-b-c"

std::vector<absl::string_view> v = {"a", "b", "c"};
std::string s = absl::StrJoin(v.begin(), v.end(), "-");
// s == "a-b-c"

std::vector<int> v = {1, 2, 3};
std::string s = absl::StrJoin(v, "-");
// s == "1-2-3"

const int a[] = {1, 2, 3};
std::string s = absl::StrJoin(a, "-");
// s == "1-2-3"

// For the map
std::map<std::string, int> m = { {"a", 1}, {"b", 2}, {"c", 3} };
std::string s = absl::StrJoin(m, ";", absl::PairFormatter("="));
// s == "a=1;b=2;c=3"

// using Lambda
std::vector<Foo> foos = GetFoos();
std::string s = absl::StrJoin(foos, ", ", [](std::string* out, const Foo& foo) {
  absl::StrAppend(out, foo.ToString());
});
```



### factory mode

```c++
// foo.h
class Foo {
 public:
  // Factory method: creates and returns a Foo.
  // May return null on failure.
  static std::unique_ptr<Foo> Create();

  // Foo is not copyable.
  Foo(const Foo&) = delete;
  Foo& operator=(const Foo&) = delete;

 private:
  // Clients can't invoke the constructor directly.
  Foo();
};

// foo.c
std::unique_ptr<Foo> Foo::Create() {
  // Note that since Foo's constructor is private, we have to use new.
  return absl::WrapUnique(new Foo());
}
```



### avoid flags

```c++
// The common use of flags in production code, especially within libraries, is a mistake. Don’t use flags unless it is truly necessary. There, we said it. To fix legacy.
```



### argument dependent lookup

```c++
namespace b {
void func(const string&);  // b::func
namespace internal {
void func(int);  // b::internal::func
namespace deep {
void test() {
  string s("hello");
  func(s);  // error: finds only b::internal::func(int).
}
}  // b::internal::deep
}  // b::internal
}  // b

namespace aspace {
struct A {};
void func(const A&);  // found by ADL name lookup on 'a'.
}  // namespace aspace

namespace bspace {
void func(int);  // found by lexical scope name lookup
void test() {
  aspace::A a;
  func(a);  // aspace::func(const aspace::A&)
}
}  // namespace bspace

namespace aspace {
struct A {};
template <typename T> struct AGeneric {};
void func(const A&);
template <typename T> void find_me(const T&);
}  // namespace aspace

namespace bspace {
typedef aspace::A AliasForA;
struct B : aspace::A {};
template <typename T> struct BGeneric {};
void test() {
  // ok: base class namespace searched.
  func(B());
  // ok: template parameter namespace searched.
  find_me(BGeneric<aspace::A>());
  // ok: template namespace searched.
  find_me(aspace::AGeneric<int>());
}
}  // namespace bspace

// Using another namespace type as argument.
```



### joining tuples

```c++
auto tup = std::make_tuple(123, "abc", 0.456);
std::string s = absl::StrJoin(tup, "-");
s = absl::StrJoin(std::make_tuple(123, "abc", 0.456), "-");

int a = 123;
std::string b = "abc";
double c = 0.456;

// Works, but copies all arguments.
s = absl::StrJoin(std::make_tuple(a, b, c), "-");
// No copies, but only works with lvalues.
s = absl::StrJoin(std::tie(a, b, c), "-");
// No copies, and works with lvalues and rvalues.
s = absl::StrJoin(std::forward_as_tuple(123, MakeFoo(), c), "-");

struct Foo {};
struct Bar {};

struct MyFormatter {
  void operator()(string* out, const Foo& f) const {
    out->append("Foo");
  }
  void operator()(string* out, const Bar& b) const {
    out->append("Bar");
  }
};

std::string s = absl::StrJoin(std::forward_as_tuple(Foo(), Bar()), "-",
                         MyFormatter());
EXPECT_EQ(s, "Foo-Bar");
```



### member initialization

```c++
class Frobber {
 public:
  Frobber() : ptr_(nullptr), length_(0) { }
  Frobber(const char* ptr, size_t length)
    : ptr_(ptr), length_(length) { }
  Frobber(const char* ptr) : ptr_(ptr) { }
 private:
  const char* ptr_;
  // length_ has a non-static class member initializer
  const size_t length_ = strlen(ptr_);
};

// =
class Frobber {
 public:
  Frobber() : ptr_(nullptr), length_(0) { }
  Frobber(const char* ptr, size_t length)
    : ptr_(ptr), length_(length) { }
  Frobber(const char* ptr)
    : ptr_(ptr), length_(strlen(ptr_)) { }
 private:
  const char* ptr_;
  const size_t length_;
};
```



### raw string print with R tag

```c++
const char concert_17_raw[] =
    "id: 17\n"
    "artist: \"Beyonce\"\n"
    "date: \"Wed Oct 10 12:39:54 EDT 2012\"\n"
    "price_usd: 200\n";
    
// better one
const char concert_17_raw[] = R"(
    id: 17
    artist: "Beyonce"
    date: "Wed Oct 10 12:39:54 EDT 2012"
    price_usd: 200)";

std::string my_string = R"foo(This contains quoted parens "()")foo";
```



### emplace

```c++
std::unique_ptr<Foo> f(new Foo(1, 2));
v1.emplace_back(new Foo(1, 2));
v1.push_back(std::move(f));

v1.emplace(v1.begin(), new Foo(1, 2));
```



### delegating & inheriting constructors

```c++
// reuse constructor
class C {
 public:
  C(int x, string s) { SharedInit(x, s); }
  explicit C(int x) { SharedInit(x, ""); }
  explicit C(string s) { SharedInit(0, s); }
  C() { SharedInit(0, ""); }
 private:
  void SharedInit(int x, string s) { … }
};

// More clean one
class C {
 public:
  C(int x, string s) { … }
  explicit C(int x) : C(x, "") {}
  explicit C(string s) : C(0, s) {}
  C() : C(0, "") {}
};

// inherite constructors
class D : public C {
 public:
  using C::C;  // inherit all constructors from C
  void NewMethod();
};
```



### using absl status

```c++
void CallFoo4() {
  absl::Status status = Foo();
  if (!status.ok()) std::cerr << status;
}
```





### temporaries, moves & copies

```c++
// Two names, it's a copy
std::vector<int> foo;
FillAVectorOfIntsByOutputParameterSoNobodyThinksAboutCopies(&foo);
std::vector<int> bar = foo;     // Yep, this is a copy.

std::map<int, string> my_map;
string forty_two = "42";
my_map[5] = forty_two;          // Also a copy: my_map[5] counts as a name.

// One name, it's a move
std::vector<int> GetSomeInts() {
  std::vector<int> ret = {1, 2, 3, 4};
  return ret;
}

// Just a move: either "ret" or "foo" has the data, but never both at once.
std::vector<int> foo = GetSomeInts();

// Not copy, like move.
std::vector<int> foo = GetSomeInts();
// Not a copy, move allows the compiler to treat foo as a
// temporary, so this is invoking the move constructor for
// std::vector<int>.
// Note that it isn’t the call to std::move that does the moving,
// it’s the constructor. The call to std::move just allows foo to
// be treated as a temporary (rather than as an object with a name).
std::vector<int> bar = std::move(foo);

// Zero name, temporary
void OperatesOnVector(const std::vector<int>& v);

// No copies: the values in the vector returned by GetSomeInts()
// will be moved (O(1)) into the temporary constructed between these
// calls and passed by reference into OperatesOnVector().
OperatesOnVector(GetSomeInts());

// Move does not move when
std::vector<int> foo = GetSomeInts();
std::move(foo); // Does nothing.
// Invokes std::vector<int>’s move-constructor.
std::vector<int> bar = std::move(foo);
```



### initialization with =, (), {}

```c++
// With list
int x{2};
std::string foo{"Hello World"};
std::vector<int> v{1, 2, 3};

// vs
int x = 2;
std::string foo = "Hello World";
std::vector<int> v = {1, 2, 3};

std::vector<std::string> strings{2}; // A vector of two empty strings.
std::vector<int> ints{2};            // A vector containing only the integer 2.

// Recommendation
int x = 2;
std::string foo = "Hello World";
std::vector<int> v = {1, 2, 3};
std::unique_ptr<Matrix> matrix = NewMatrix(rows, cols);
MyStruct x = {true, 5.0};
MyProto copied_proto = original_proto;

// Instead
// Bad code
int x{2};
std::string foo{"Hello World"};
std::vector<int> v{1, 2, 3};
std::unique_ptr<Matrix> matrix{NewMatrix(rows, cols)};
MyStruct x{true, 5.0};
MyProto copied_proto{original_proto};

// Recommendation
Frobber frobber(size, &bazzer_to_duplicate);
std::vector<double> fifty_pies(50, 3.14);

// Bad code
// Could invoke an intializer list constructor, or a two-argument constructor.
Frobber frobber{size, &bazzer_to_duplicate};
// Makes a vector of two doubles.
std::vector<double> fifty_pies{50, 3.14};

// Using {} if necessary
class Foo {
 public:
  Foo(int a, int b, int c) : array_{a, b, c} {}

 private:
  int array_[5];
  // Requires {}s because the constructor is marked explicit
  // and the type is non-copyable.
  EventManager em{EventManager::Options()};
};

// Never mix {}s and auto.
// Bad code
auto x{1};
auto y = {2}; // This is a std::initializer_list<int>!
```



### retired flags

```c++
1. remove FLAGS_frobber from code
2. change ABSL_FLAG(type, frobber, "default", "Which frobber to use?"); to ABSL_RETIRED_FLAG(type, frobber, "default", "retired");
3. Wait for binary release
4. Remove from configureation.
5. Remove the retired flag.
  
```



### absl::span

```c++
// As function paramter
void TakesVector(const std::vector<int>& ints);
void TakesSpan(absl::Span<const int> ints);

void PassOnlyFirst3Elements() {
  std::vector<int> ints = MakeInts();
  // We need to create a temporary vector, and incur an allocation and a copy.
  TakesVector(std::vector<int>(ints.begin(), ints.begin() + 3));
  // No copy or allocations are made when using Span.
  TakesSpan(absl::Span<const int>(ints.data(), 3));
}

void PassALiteral() {
  // This creates a temporary std::vector<int>.
  TakesVector({1, 2, 3});
  // Span does not need a temporary allocation and copy, so it is faster.
  TakesSpan({1, 2, 3});
}
void IHaveAnArray() {
  int values[10] = ...;
  // Once more, a temporary std::vector<int> is created.
  TakesVector(std::vector<int>(std::begin(values), std::end(values)));
  // Just pass the array. Span detects the size automatically.
  // No copy was made.
  TakesSpan(values);
}

// Const correctness for vector of pointers
// Bad code
class DontDoThis {
 public:
  // Don’t modify my Foos, pretty please.
  const std::vector<Foo*>& shallow_foos() const { return foos_; }

 private:
  std::vector<Foo*> foos_;
};

void Caller(const DontDoThis& my_class) {
  // Modifies a foo even though my_class is a reference-to-const
  my_class->foos()[0]->SomeNonConstOp();
}
// Good code
class DoThisInstead {
 public:
  absl::Span<const Foo* const> foos() const { return foos_; }

 private:
  std::vector<Foo*> foos_;
};

void Caller(const DoThisInstead& my_class) {
  // This one doesn't compile.
  // my_class.foos()[0]->SomeNonConstOp();
}

// Buffer overflow prevention
// Bad code
void BadUser() {
  int src[] = {1, 2, 3};
  int dest[2];
  memcpy(dest, src, ABSL_ARRAYSIZE(src) * sizeof(int)); // oops! Dest overflowed.
}
// A simple example, but takes advantage that the sizes of the Spans are known
// and prevents the above mistake.
template <typename T>
bool SaferMemCpy(absl::Span<T> dest, absl::Span<const T> src) {
  if (src.size() > dest.size()) {
    return false;
  }
  memcpy(dest.data(), src.data(), src.size() * sizeof(T));
  return true;
}

void GoodUser() {
  int src[] = {1, 2, 3}, dest[2];
  // No overflow!
  SaferMemCpy(absl::MakeSpan(dest), absl::Span<const int>(src));
}
```



### Callsite and readability and bool

```c++
// Like this
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, false);
}

// bad
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, false /* preserve flags */);
}

// better
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, /*remove_flags=*/false);
}

int main(int argc, char* argv[]) {
  const bool remove_flags = false;
  ParseCommandLineFlags(&argc, &argv, remove_flags);
}

// Avoid to use bool, use enum instead.
enum ShouldRemoveFlags { kDontRemoveFlags, kRemoveFlags };

void ParseCommandLineFlags(int* argc, char*** argv, ShouldRemoveFlags remove_flags);

int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, kDontRemoveFlags);
}

// Better
enum class ShouldRemoveFlags { kNo, kYes };
…
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, ShouldRemoveFlags::kNo);
}
```



### nonmember interface etiquette

```c++
namespace library {
struct Letter {};

void good(Letter);
}  // namespace library

// bad is improperly placed in global namespace
void bad(library::Letter);

namespace client {
void good();
void bad();

void test(const library::Letter& x) {
  good(x);  // ok: 'library::good' is found by ADL.
  bad(x);  // oops: '::bad' is hidden by 'client::bad'.
}

}  // namespace client
```



### return values, references and lifetimes

```c++
1. Returning string, initializing string: This is usually RVO and is guaranteed to be, at worst, a move for modern types (See TotW 77).
2. Returning string& or const string&, initializing string: This is a copy (there must be some long-living object which we are returning a reference to, so once we initialize a new string there are two names for that data, hence a copy. See TotW 77). Sometimes this is valuable, like if you need your string to outlast the lifetime guarantees provided by the function.
3. Returning string, initializing string&: This won’t compile, as you cannot bind a reference to a temporary.
4. Returning const string&, initializing string&: This won’t compile, as you’ve dropped the const inappropriately.
5. Returning const string&, initializing const string&: This is no cost (you’re effectively returning just a pointer). However, you have inherited any existing lifetime restrictions: how long is that reference good for? Most accessor methods that return a reference are returning a member - at most, the reference can be valid for the life of the containing object.
6. Returning string&, initializing string&: This is the same as #5, but with the additional caveat: the returned reference is non-const, so any modifications to your reference will be reflected in the source.
7. Returning string&, initializing const string&: The same as #5.
8. Returning string, initializing const string&: You’d think, given #3, that this wouldn’t work. However, the language has special-case support for this: if you initialize a const T& with a temporary T, that T (in this case string) is not destroyed until the reference goes out of scope (in the common case of automatic or static variables).

const string& name = obj.GetName();
std::unique_ptr<Consumer> consumer(new Consumer(name));  
// Is GetName() returning by value or by reference?
// Does the constructor for Consumer take string, const string& or string_view?
// Does the constructor have any lifetime requirements placed on that parameter? (If it isn’t just string.)  
```



```c++
// If return is local const T&, is not work.
1. This doesn’t work when assigning to T&, it must be const T&. (It’s a compilation error.)
2. This doesn’t work if there is a (non-polymorphic) type conversion at play. For instance, assigning to const absl::string_view& from string does not extend the lifetime of the string. (You also don’t want const absl::string_view& in the first place, but that’s a separate issue).
3. This doesn’t work when you’re getting the subobject indirectly: the compiler doesn’t look through function calls (getters or the like). The subobject form only works when you’re directly assigning from a public member variable subobject of the temporary. (So it doesn’t come up often because we don’t have many expressions that return temporary structs.)
4. The case where type conversion is allowed is when assigning to a T& from a temporary of U when T is a parent class of U. Please don’t do that: it’s even more confusing for readers than the other cases.
  
If the lifetime of the temporary is extended, it will last until the reference goes out of scope. If the lifetime of the temporary isn’t extended in the above fashion, the T being referred to is destroyed at the end of the statement (when we get to the next ;).
  
There are subtle cases where lifetime extension is happening and is necessary and beneficial (like ranged-for over a temporary container), but ag
  
std::vector<int> GetInts();
for (int i : GetInts()) { }  // lifetime extension on the vector is important

// Return string_views of size 1 for each char in this string.
std::vector<absl::string_view> Explode(const string& s);

// Lifetime extension kicks in on the vector, but *not* on the temporary string!
for (absl::string_view s : Explode(StrCat("oo", "ps"))) { }  // WRONG

MyProto GetProto();

// Lifetime extension *doesn't work* here: sub_protos (a repeated field)
// is destroyed by MyProto going out of scope, and the lifetime extension rules
// don't kick in here to magically lifetime extend the MyProto returned by
// GetProto().  The sub-object lifetime extension only works for simple
// is-a-member-of relationships: the compiler doesn't see that sub_protos()
// itself returning a reference to an sub-object of the outer temporary.
for (const SubProto& p : GetProto().sub_protos()) { }  // WRONG
```



### avoid std::bind

```c++
// bind parameter and compiling are easy to fail.
// std::bind() disables one of the basic compile time checks

// Applying std::bind() to a type you don’t control is always a bug.

// Avoid std::bind. Use a lambda or absl::bind_front instead.
```



### const 

```c++
void F(int);                     // 1: declaration of F(int)
void F(const int);               // 2: re-declaration of F(int)
void F(int) { /* ... */ }        // 3: definition of F(int)
void F(const int) { /* ... */ }  // 4: error: re-definition of F(int)

// const type-specifiers buried within a parameter type specification are significant and can be used to distinguish overloaded function declarations
// Not ignored
void F(const int* x);                  // 1
void F(const int& x);                  // 2
void F(std::unique_ptr<const int> x);  // 3
void F(int* x);                        // 4

// const is meaningless and ignored in declaration.
void F(const int x);          // 1: declares F(int)
void F(int* const x);         // 2: declares F(int*)
void F(const int* const x);   // 3: declares F(const int*)

void PrintX(const int x);
int main()
{
    PrintX(6);
    return 0;
}
void PrintX(int x) {
    x = 7;
    cout << x << endl;
}
```



### push_back vs insert vs emplace

```c++
// emplace_back will call constructors. But faster with implicit construct.
// push_back will be more error free. Will be safer and readability.
// So if both support with performance, use push_back. Same as insert vs emplace for set. 
```



### const reference vs pointer

```c++
// Bad case for const reference
class Foo {
 public:
  explicit Foo(const std::string& content) : content_(content) {}
  const std::string& content() const { return content_; }

 private:
  const std::string& content_;
};

void Func() {
  Foo foo("something");
  std::cout << foo.content();  // BOOM!
}

class Foo {
 public:
  // Do not forget this comment:
  // Does not take ownership of content, which must refer to a valid string that
  // outlives this object.
  explicit Foo(const std::string* content) : content_(content) {}
  const std::string& content() const { return *content_; }

 private:
  const std::string* const content_;  // not owned, can't be null
};

std::string GetString();
void Func() {
  Foo foo1(&GetString());  // error: taking the address of a temporary of
                           // type 'std::string'
  Foo foo2(&"something");  // error: no matching constructor for initialization
                           // of 'Foo'
}

void Func2() {
  std::string content = GetString();
  Foo foo(&content);
}

// With const reference
class Baz {
 public:
  // Does not take any ownership, and all pointers must refer to valid objects
  // that outlive the one constructed.
  explicit Baz(const Arg1* arg1, Arg2* arg2) : arg1_(*arg1), arg2_(*arg2) {}

 private:
  // It is now clear that we do not have ownership and that the references can't
  // be null.
  const Arg1& arg1_;
  Arg2& arg2_;  // Yes, non-const references are style-compliant!
};
// It is still OK to pass an argument by const reference to a constructor if the argument is copied, or just used in the constructor and no reference to it is kept in the constructed object. In other cases, consider passing arguments by pointers (to const or not). Also remember that if you are actually transferring the ownership of an object, it should be passed as a std::unique_ptr.
```



### copy vs pass-by-value

```c++
class Widget {
 public:
  …

 private:
  string name_;
};

// First constructor version
explicit Widget(const std::string& name) : name_(name) {}

// Second constructor version
explicit Widget(std::string name) : name_(std::move(name)) {}

Widget widget(absl::StrCat(bar, baz)); // second is better.

// When to use copy
// copy sometimes is risky with move. Sometime there is no need copy. 
// Generally speaking, you should prefer simpler, safer, more readable code, and only go for something more complex if you have concrete evidence that the complex version performs better and that the difference matters. That principle certainly applies to this technique: passing by const reference is simpler and safer, so it’s still a good default choice. However, if you’re working in an area that’s known to be performance-sensitive, or your benchmarks show that you’re spending too much time copying function parameters, passing by value can be a very useful tool to have in your toolbox.
```



### using-declarations & namespace aliases

```c++
// in .cc file
namespace example {
namespace makers {
namespace {

using ::otherlib::BazBuilder;
using ::mylib::BarFactory;
namespace abc = ::applied::bitfiddling::concepts;

// Private helper code here.

}  // namespace

// Interface implementation code here.

}  // namespace makers
}  // namespace example

// Never declare namespace aliases or convenience using-declarations at namespace scope in header files, only in `.cc` files.
// Declare namespace aliases and using-declarations inside the innermost namespace, whether named or anonymous. (Do not add an anonymous namespace just for this purpose.)
// When declaring namespace aliases and using-declarations, use fully qualified names (with leading `::`) unless you are referring to a name inside the current namespace.

// Scope of the alias
using ::foo::Quz;

namespace example {
namespace util {

using ::foo::Bar;
// to avoid to be broken, this could be unamed namespaces.
  
// tip 3
namespace example {
namespace util {

using foo::Bar;
// the code is relying on the existence of ::foo::Bar and also on the non-existence of namespaces ::example::foo and ::example::util::foo. This brittleness can be avoided by qualifying the used name fully: using ::foo::Bar.
namespace example {
namespace util {
namespace internal {

struct Params { /* ... */ };

}  // namespace internal

using internal::Params;  // OK, same as ::example::util::internal::Params  
```



### return value is untouchable(not clear)

```c++
MyStatus DoSomething() {
  MyStatus status;
  auto log_on_error = RunWhenOutOfScope([&status] {
    if (!status.ok()) LOG(ERROR) << status;
  });
  status = DoA();
  if (!status.ok()) return status;
  status = DoB();
  if (!status.ok()) return status;
  status = DoC();
  if (!status.ok()) return status;
  return status;
}

// Never access (read or write) the return variable of a function after the return statement has run. Unless you take great care to do it correctly, the behavior is unspecified.

// Return variables are implicitly accessed by destructors after they have been copied or move. This tip only applies when you are returning a non-reference local variable. Returning any other expression will not trigger this problem.

// Change
yStatus DoSomething() {
  MyStatus status_no_nrvo;
  // 'status' is a reference so NRVO and all associated optimizations
  // will be disabled.
  // The 'return status;' statements will always copy the object and Logger
  // will always see the correct value.
  MyStatus& status = status_no_nrvo;
  auto log_on_error = RunWhenOutOfScope([&status] {
    if (!status.ok()) LOG(ERROR) << status;
  });
  status = DoA();
  if (!status.ok()) return status;
  status = DoB();
  if (!status.ok()) return status;
  status = DoC();
  if (!status.ok()) return status;
  return status;
}

std::string EncodeVarInt(int i) {
  std::string out;
  StringOutputStream string_output(&out);
  CodedOutputStream coded_output(&string_output);
  coded_output.WriteVarint32(i);
  return out;
}

// A good solution is to add a block and restrict the function to only return after the block is finished. Like this:
std::string EncodeVarInt(int i) {
  std::string out;
  {
    StringOutputStream string_output(&out);
    CodedOutputStream coded_output(&string_output);
    coded_output.WriteVarint32(i);
  }
  // At this point the streams are destroyed and they already flushed.
  // We can safely return 'out'.
  return out;
}
```



### test fixtures, clarity & dataflow

```c++
class FrobberTest : public ::testing::Test {
 protected:
  void ConfigureExampleA() {
    example_ = "Example A";
    frobber_.Init(example_);
    expected_ = "Result A";
  }

  void ConfigureExampleB() {
    example_ = "Example B";
    frobber_.Init(example_);
    expected_ = "Result B";
  }

  Frobber frobber_;
  string example_;
  string expected_;
};

TEST_F(FrobberTest, CalculatesA) {
  ConfigureExampleA();
  string result = frobber_.Calculate();
  EXPECT_EQ(result, expected_);
}

TEST_F(FrobberTest, CalculatesB) {
  ConfigureExampleB();
  string result = frobber_.Calculate();
  EXPECT_EQ(result, expected_);
}

// to make this
TEST(FrobberTest, CalculatesA) {
  Frobber frobber;
  frobber.Init("Example A");
  EXPECT_EQ(frobber.Calculate(), "Result A");
}

TEST(FrobberTest, CalculatesB) {
  Frobber frobber;
  frobber.Init("Example B");
  EXPECT_EQ(frobber.Calculate(), "Result B");
}

// example 2
class BobberTest : public ::testing::Test {
 protected:
  void SetUp() override {
    bobber1_ = PARSE_TEXT_PROTO(R"(
        id: 17
        artist: "Beyonce"
        when: "2012-10-10 12:39:54 -04:00"
        price_usd: 200)");
    bobber2_ = PARSE_TEXT_PROTO(R"(
        id: 21
        artist: "The Shouting Matches"
        when: "2016-08-24 20:30:21 -04:00"
        price_usd: 60)");
  }

  BobberProto bobber1_;
  BobberProto bobber2_;
};

TEST_F(BobberTest, UsesProtos) {
  Bobber bobber({bobber1_, bobber2_});
  SomeCall();
  EXPECT_THAT(bobber.MostRecent(), EqualsProto(bobber2_));
}

// change to
BobberProto RecentCheapConcert() {
  return PARSE_TEXT_PROTO(R"(
      id: 21
      artist: "The Shouting Matches"
      when: "2016-08-24 20:30:21 -04:00"
      price_usd: 60)");
}
BobberProto PastExpensiveConcert() {
  return PARSE_TEXT_PROTO(R"(
      id: 17
      artist: "Beyonce"
      when: "2012-10-10 12:39:54 -04:00"
      price_usd: 200)");
}

TEST(BobberTest, UsesProtos) {
  Bobber bobber({PastExpensiveConcert(), RecentCheapConcert()});
  SomeCall();
  EXPECT_THAT(bobber.MostRecent(), EqualsProto(RecentCheapConcert()));
}

// tips
// Avoid fixtures where reasonable. Sometimes it’s not.
// If you are using fixtures, try to avoid fixture member variables. It is far too easy to start operating on those in ways akin to globals: data flow is hard to track since absolutely any code path in the fixture may modify the member.
// If you’ve got variables that need complex initialization that would make each test hard to read, consider a helper function (not part of the fixture) that documents that initialization and returns the object directly.
// If you must have fixtures that contain member variables, try to avoid methods that operate on those members directly: pass them in as parameters whenever possible to make the dataflow clear.
// Try to write tests before writing headers: if you start with a usage that is pleasant to test, your API is usually better, and your tests are almost always clearer.
```



### abs::optional & std::unique_ptr

```c++
// absl::optiona;
// It’s less obvious for the reader where object construction and destruction occur.
// There is a risk of accessing an object which does not exist.

// unique_ptr
// Increased cognitive load on the reader:
// The pointer introduces an additional level of indirection, which requires a heap allocation, and is not friendly to CPU caches; Whether this matters or not depends a lot on particular use cases.

// Conclusion: Prefer bare object, if it works for your case. Otherwise, try absl::optional. As a last resort, use std::unique_ptr.

// Table: https://abseil.io/tips/123
```



### why make_unique

```c++
// When possible, ownership is best expressed in the type system. This allows reviewers to verify correctness
// Somewhat like the reasoning for preferring push_back() over emplace_back() (TotW 112), absl::make_unique() directly expresses the intent and can only do one thing
// If all allocations are handled via absl::make_unique() or factory calls, that leaves absl::WrapUnique() for the implementation of those factory calls, for code interacting with legacy methods that don’t rely on std::unique_ptr for ownership transfer, and for rare cases that need to dynamically allocate with aggregate initialization
// Readability
// in a factory function that uses a non-public constructor
```



### namespace naming

```c++
// Always fully qualify everything outside of the current namespace. This is very verbose and sort of weird: nothing in C++ (including the standard library) is written with leading :: on every symbol.
// Build some tooling to identify introduction of new namespaces and ensure that it doesn’t overlap with any other namespace in the same hierarchy. That is, do not add search::bar if there is a ::bar or a search::foo::bar.
// Don’t nest deeply: a single top-level namespace per project gets the same result without long/complicated names, with less exposure to accidents, without causing surprise for new engineers, and without the need to build any tooling.

// Have a database of some form for a codebase to identify the unique namespaces.
// When introducing a new namespace, use that database and introduce it as a top-level.
// If for some reason the above is impossible, never ever introduce a sub-namespace that matches a well-known top-level namespace. No sub-namespaces for absl, testing, util, etc. Try to give sub-namespaces unique names that are unlikely to collide with future top-levels.
// When declaring namespace aliases and using-declarations, use fully qualified names, unless you are referring to a name inside the current namespace, as per TotW 119.
// For code in util or other commonly-abused namespaces, try to avoid full qualification, but qualify if necessary

// This using-directive has introduced a namespace alias proto in the global namespace.
// bad
namespace totw {
namespace example {
namespace {

TEST(MyTest, UsesUsingDirectives) {
  using namespace ::testing;
  Sequence seq;  // ::testing::Sequence
  WallTimer timer;  // ::WallTimer
  ...
}

}  // namespace
}  // namespace example
}  // namespace totw
```



### default & private constructor

```c++
class E {
 public:
  E();  // promises to have a default constructor, but...
 private:
  const int i;  // const => must always be initialized.
};
inline E::E() = default;  // compilation error here: would not initialize `i`

class F {
 public:
  F();  // promises to have a default constructor
 private:
  std::unique_ptr<int> p;  // std::unique_ptr has a default constructor
};
inline F::F() = default;  // works as expected

// Private constructor
class Widget {
 public:
  static std::unique_ptr<Widget> Make() {
    return absl::make_unique<Widget>(GenerateId());
  }

 private:
  Widget(int id) : id_(id) {}
  static int GenerateId();

  int id_;
}
// Fail because the make_unique can not access private Widget constructor. And the return absl::WrapUnique(new Widget(...)); works.

// For the shared_ptr, this could be used.
class Widget {
  class Token {
   private:
    Token() {}
    friend Widget;
  };

 public:
  static std::shared_ptr<Widget> Make() {
    return std::make_shared<Widget>(Token{}, GenerateId());
  }

  Widget(Token, int id) : id_(id) {}

 private:
  static int GenerateId();

  int id_;
};

// From C++17 variables can be marked as inline, ensuring that there is only a single copy of the variable.
// in foo.h
// inline constexpr int kMyNumber = 42;
// inline constexpr absl::string_view kMyString = "Hello

// Good style
// Declared in foo.h
class Foo {
 public:
  static constexpr int kMyNumber = 42; // will not be changed by other objects. have out access.
  static constexpr char kMyHello[] = "Hello";
};

// constexpr guarantees safe constant initialization as well as safe (trivial) destruction. Any constexpr variable is entirely fine when defined in a .cc file, but is problematic in header files for reasons explained earlier.
// ABSL_CONST_INIT guarantees safe constant initialization. Unlike constexpr, it does not actually make the variable const, nor does it ensure the destructor is trivial, so care must still be taken when declaring static variables with it. See again https://google.github.io/styleguide/cppguide.html#Static_and_Global_Variables.
// Otherwise, you’re most likely best off using a static variable within a function and returning it. See http://go/cppprimer#static_initialization, and the “ordinary function” example shown earlier.
```



### implicit to bool

```c++
// Compare pointer types to nullptr (especially if the pointed-at type is implicitly convertible to bool).
// test container emptiness with boolean functions like absl::optional<T>::has_value() (especially if the contained type is implicitly convertible to bool). Use the optional initializer form for if to limit the scope of variables (Tip #165). Remember call-only interfaces though
using like; // if (ptr != nullptr) is better.
```



### explicit & deleted & initialize & switch & overload sets

```c++
// Copy constructors and move constructors should never be explicit.
// It is tempting to try to use rvalue-references or reference qualifiers in conjunction with =delete to provide a more “user friendly” API, enforcing lifetimes or preventing optimization problems. In practice, those are usually bad temptations
// No
class Request {
  ...

  // The provided Context must live as long as the current Request.
  void SetContext(const Context& context);
  void SetContext(Context&& context) = delete;
};

// Any function can be deleted, including non-member functions (in contrast to =default, which works only with special member functions).
// Functions must be deleted on the first declaration only (unlike =default).

Be explicit about the value to which scalar types are being initialized instead of relying on zero-initialization.
Treat all instances of scalar types as having indeterminate values until you explicitly initialize or assign to them.
If a member has a sensible default, and the class has multiple constructors, use a default member initializer to ensure it isn’t left uninitialized
  
// switch should be exhaustive
// Exhaustive switch statements can be an excellent tool for ensuring that all enumerators are explicitly handled
  
// Overload sets are a simple idea conceptually, but prone to abuse when not well understood - don’t produce overloads where anyone might need to know which function was chosen.
```



### when local && if switch init

```c++
// Their name adds useful documentation.
// They simplify excessively complicated expressions.
// They factor out a repeated expression to make it clear to humans (and to a lesser extent compilers) that it’s the same value every time.
// An object’s lifetime needs to extend across multiple statements (for example, because references to the object are retained beyond the end of a single statement or because the variable holds a value that is updated during its lifetime).

// C++ 17 if and swithc could be init in body
if (auto [iter, ins] = m.try_emplace(key, data); ins) {
  use(iter->second);
} else {
  std::cerr << "Key '" << key << "' already exists.";
}
// Use the new if (init; cond) and switch (init; cond) syntax when you need a new variable for use within the if or switch statement that is not needed outside of it. This simplifies the ambient code. Moreover, since the variable’s scope is now small, its name can be shorter, too.
```



### inline && avoid sentinel values && group arguments in struct

```c++
// C++
inline constexpr absl::string_view kHelloWorld = "Hello World."; // make one copy.
A static constexpr data member of a class is implicitly inline from C++17. 

// using optional to avoid sentinel values.  
  
// using struct to wrap the arguments
// For functions which take multiple arguments which may be confused by the caller or where you want to specify default arguments without having to worry about the order, strongly consider using option structs to increase both convenience and code clarity.
// Or call sth like this.
PrintDouble(5.0, "my_value=",
            /*precision=*/2,
            /*thousands_separator=*/',',
            /*decimal_separator=*/'.',
            /*scientific=*/false);  
```



### return value or output parameters && StatusOr

```c++
// Prefer to return value to output parameters
// Using a struct to return multiple values from a function.

// Accessing the value by StatusOr
```



### prefer unnamed namespace && unique_ptr

```c++
// Making it easy for a reader to look up the definition (since it’s in the same file as usage and above the first usage).
// Placing the documentation, declaration, and definition in a single location (versus two locations for functions which are declared in a header file).
// Isolating it from other source files, making it easier to refactor.
// Removing the need to worry about meaningful const as there is no separate declaration.
// Keeping them in the same place as other implementation helpers for a library, such as convenience aliases and local types. See Tip of the Week #119: Using-declarations and Namespace Aliases.
// Providing side benefits, such as allowing a type to also be moved to an unnamed namespace (if it is only referenced in a single source file).
// Inputs and outputs are clear since they are (much more likely to be) specified via arguments or the return value. Note that a method may read any member variable and a non-const method may modify any non-const member. Conversely, a non-member function may only read or modify according to its interface (except for globals).
// The class API is simpler and shorter, and hence easier to read—unnecessary private methods may make it difficult to find inheritance-related private declarations or declarations after the class

// unique_ptr shoule be moved if not moved, thinking about memory leakage and necessary.
```

