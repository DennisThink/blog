---
title: "C++的历史以及重要版本"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
url: instroduce-of-cpp-history-and-main-version
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

##　1. 起源

C++的历史可以追溯到1979年，当时Bjarne Stroustrup（译者注：C++之父）正在准备他的博士毕业论文，他有机会使用一种叫做Simula 的语言。顾名思义，Simula语言的主要作用是仿真。Simula 67是Simula语言的一种变种，被公认是首款支持面向对象的语言。Stroustrup发现面向对象的思想对于软件开发非常有用，但是因Simula语言执行效率低，其实用性不强。

不久之后，Stroustrup开始着手“C with Classes”的研发工作，“C with Classes”表明这种新语言是在C基础上研发的，是C语言的超集。C语言以其高可移植性而广受好评，且程序执行速度以及底层函数的性能不受程序移植的影响，Stroustrup要做的就是将面向对象的思想引入C语言。新语言的初始版本除了包括C语言的基本特征之外，还具备类、简单继承、内联机制、函数默认参数以及强类型检查等特性。

Bjarne Stroustrup的第一款“C with classes”编译器叫Cfront，这个名字源自一个叫做Cpre的C编译器。Cfront的机理是把“C with classes”的代码翻译成原生C代码。颇为有趣的一点是Cfront源码大部分是由“C with Classes”编写，这使得Cfront成为了一种自足执行的编译器（可以编译自身源码的编译器）。由于很难整合C++的异常机制，Cfront在1993年退出了历史的舞台，但是它对以后C++编译器以及Unix操作系统的实现都产生了深远的影响。

1983年，“C with Classes”语言更名为C++。C语言中“++”运算子的作用是对一个变量进行递增操作，由此我们多少可以知晓Stroustrup对这种新语言的定位。这个时期，许多重要的特性被加入，其中包括虚函数、函数重载、引用机制（符号为&）、const关键字以及双斜线的单行注释（从BCPL语言引入）。

1985年，Stroustrup的C++参考手册《C++ Programming Language》出版，同年，C++的商业版本问世。由于当时C++并没有正式的语言规范，因此《C++ Programming Language》成了业界的重要参考。1989年，C++再次版本更新，这次更新引入了多重继承、保护成员以及静态成员等语言特性。

## 2.重要版本
### 2.1 C++98 
1998 C++98 (ISO/IEC 14882:1998)
新特性： RTTI （ dynamic_cast 、 typeid ）、协变返回类型、转型运算符、 mutable 、 bool 、条件中的声明、模板实例化、成员模板、 export
库添加：容器、算法、迭代器、函数对象（基于 STL ）、 locale 、 bitset 、 valarray 、 auto_ptr 、模板化字符串 、 iostream 和 complex。

### 2.2 C++03(05)
来自 Boost ：引用包装器、智能指针、成员函数、 result_of 、 bind 、 function 、类型特性、随机数、数学特殊函数、 tuple 、 array 、无序容器（包含 hash ）和正则表达式。
来自 C99 ： C99 中新的来自 math.h 的数学函数、空格字符类、浮点环境、 hexfloat I/O 操纵符、固定大小整数类型、 long long 类型、 va_copy 、 snprintf() 和 vfscanf() 函数族及 printf() 和 scanf() 函数族的 C99 转换指定。

### 2.3 C++11 
新语言特性： auto 和 decltype 、默认化和被删除的函数、 final 和 override 、尾随返回类型、右值引用、移动构造函数/移动赋值、有作用域 enum 、 constexpr 和字面类型、列表初始化、委托和继承的构造函数、花括号或等号初始化器、 nullptr 、 long long 、 char16_t 与 char32_t 、类型别名、变参数模板、广义化联合体、广义化 POD 、 Unicode 字符串字面量、用户定义字面量、属性、 lambda 表达式、 noexcept 、 alignof 与 alignas 、多线程内存模型、线程局域存储、 GC 接口、范围 for （基于 Boost 库）、静态断言（基于 Boost 库）
新库特性：原子操作库、 emplace() 和遍及既存标准库所有部分的其他右值引用使用、 std::initializer_list 、有状态和有作用域分配器、 forward_list 、 chrono 库、 ratio 库、新算法、 Unicode 转换平面
来自 TR1 ：所有 TR1 ，除了特殊函数。
来自 Boost ：线程库、 exception_ptr 、 error_code 和 error_condition 、迭代器改进（ std::begin 、 std::end 、 std::next 、 std::prev ）
来自 C ： C 风格 Unicode 转换函数

### 2.4 C++14

新语言特性：变量模板、多态 lambda 、 lambda 捕获表达式、 new/delete 消除、放松 constexpr 函数上的限制、二进制字面量、数位分隔符、函数的返回类型推导、带花括号或等号初始化器的类的聚合初始化。
新库特性： std::make_unique 、 std::shared_timed_mutex 及 std::shared_lock 、 std::integer_sequence 、 std::exchange 、 std::quoted 以及许多既存库设施的小改进，例如某些算法的双范围重载、类型特性的类型别名、用户定义的 string 、 duration 和复数字面量等。

### 2.5 C++17

语言更改：折叠表达式、类模板实参推导、 auto 非类型模板形参、编译时 if constexpr 、 inline 变量、结构化绑定、 if 和 switch 的初始化器、 u8-char 、简化嵌套命名空间、 using 声明能声明多个名称、令 noexcept 为类型系统的部分、新的求值顺序规则、带显式对齐的分配函数、受保证的复制消除、 __has_include 、 *this 的 lambda 捕获、 constexpr lambda 、属性命名空间不必重复、新属性 [[fallthrough]] 、 [[nodiscard]] 及 [[maybe_unused]] 。移除三标符、 register 关键词、 bool 自增
库更改： std::variant 、 std::launder 、未初始化内存工具（ std::destroy_at/std::destroy/std::destroy_n 、 std::uninitialized_move 、 std::uninitialized_value_construct 等）、 map/set extract 及 map/set merge 、 byte 、 std::make_from_tuple 、 std::to_chars/from_chars 、文件系统中的相对路径支持（ proximate 、 relative 、 weakly_canonical ）、文件系统中的目录入口缓存、相接迭代器、非成员 size/empty/data 、 map/unordered_map try_emplace 及 insert_or_assign 、 uncaught_exceptions 、变参数 lock_guard 、 as_const 、 conjunction/disjunction/negation 、类型特性变量 xxx_v 、通透的 owner_less 、 duration 和 time_point 的舍入函数、 std::shared_ptr 的数组支持、 not_fn 、 weak_from_this 、 is_always_lock_free 、 is_swappable 、 clamp 、 3D hypot 、缓存线接口、 is_invocable 、 is_aggregate 、 has_unique_object_representations 。弃用 std::iterator 、 std::raw_storage_iterator 、 std::get_temporary_buffer 、 std::is_literal_type 、 std::result_of 、  codecvt> 的全体。移除 auto_ptr 、弃用的函数对象、 std::random_shuffle 、 std::unexpected 、过时的 iostream 别名。
来自 TS ：文件系统库、库基础 v1 （包含 optional 、 any 、 string_view 、多态分配器、搜索器、 apply ）、并行 v1 （包含执行策略、 reduce 、 inclusive_scan 、 exclusive_scan ），但移除 exception_list 。来自特殊数学 IS ：特殊数学函数，来自库基础 v2 ： std::gcd 、 std::lcm
来自 C11 ： std::aligned_alloc 、 std::timespec_get

### 2.6 C++20

语言更改：概念、三路比较运算符  => 、指代初始化器、范围 for 中的初始化语句和初始化器、 [[no_unique_address]] 、 [[likely]] 、 [[unlikely]] 、 lambda 捕获中的包展开、在许多语境中移除用 typename 对类型消歧义的要求、 [[expects: ...]] 、 [[ensures: ...]] 、 [[assert: ...]] 。
库更改：日历和时区库、 std::span 、 std::endian 、 make_shared 的数组支持、 std::remove_cvref 、 std::to_address 、浮点及 shared_ptr 原子类型、  version> 、 std::osyncstream 、  algorithm> 、  utility> 、  complex> 的 constexpr 、 std::string(_view)::starts_with 及 ends_with 。