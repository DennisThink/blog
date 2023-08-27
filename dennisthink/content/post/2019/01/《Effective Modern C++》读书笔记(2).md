---
title: "《Effective Modern C++》读书笔记(2)"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
url: /blog/note/effective-modern-cpp-learn-two/
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

Item2 理解auto 的类型推导

```auto```  的类型推导与```template```  的类型推导类似，主要的区别在于对于 ```std::initialize_list```  的区别。
在这个子项中的类型推导我们用到了```boost```  的1.67的版本。

具体的代码如下:

```cpp  {linenos=table}
#include  <iostream>
#include  <typeinfo>
#include  <boost/type_index.hpp>

void func(int,double)
{
}
int main(int argc,char * argv[])
{
    using boost::typeindex::type_id_with_cvr;
    //Case1:type specifier is a pointer or a reference but not a universal reference.
    auto x = 27;
    const auto cx = x;
    const auto& crx = x;
    auto& rx = x;

    std::cout  << "x Type is " << type_id_with_cvr<decltype(x)>().pretty_name() << std::endl;
    std::cout  << "cx Type is " << type_id_with_cvr<decltype(cx)>().pretty_name() << std::endl;
    std::cout  << "crx Type is " << type_id_with_cvr<decltype(crx)>().pretty_name() << std::endl;
    std::cout  << "rx Type is " << type_id_with_cvr<decltype(rx)>().pretty_name()  <<std::endl;

    auto&& uref1 = x;
    auto&& uref2 = cx;
    auto&& uref3 = rx;

    std::cout  << "uref1 Type is " << type_id_with_cvr<decltype(uref1)>().pretty_name() << std::endl;
    std::cout  << "uref2 Type is " << type_id_with_cvr<decltype(uref2)>().pretty_name() << std::endl;
    std::cout  << "uref3 Type is " << type_id_with_cvr<decltype(uref3)>().pretty_name() << std::endl;

    const char name[]="Hello Dennis";
    auto arr1 = name;
    auto& arr2 = name;

    std::cout  << "name Type is " << type_id_with_cvr<decltype(name)>().pretty_name() << std::endl;
    std::cout  << "arr1 Type is " << type_id_with_cvr <decltype(arr1)>().pretty_name() << std::endl;
    std::cout <<  "arr2 Type is " << type_id_with_cvr<decltype(arr2)>().pretty_name() << std::endl;

    auto func1 = func;
    auto& func2 = func;

    auto lamdaFunc = []()->void {return;};

    std::cout <<  "func1 Type is "  <<type_id_with_cvr<decltype(func)>().pretty_name() << std::endl;
    std::cout  << "func1 Type is " << type_id_with_cvr<decltype(func1)>().pretty_name() << std::endl;
    std::cout <<  "func2 Type is "  <<type_id_with_cvr<decltype(func2)>().pretty_name() << std::endl;
    std::cout  << "lamdaFunc Type is "<<  type_id_with_cvr<decltype(lamdaFunc)>().pretty_name() << std::endl;

    int x1 = 27;
    int x2(27);
    int x3={27};
    int x4{27};

    std::cout  << "x1 Type is " << type_id_with_cvr<decltype(x1)>().pretty_name()  <<std::endl;
    std::cout  << "x2 Type is " << type_id_with_cvr<decltype(x2)>().pretty_name()  <<std::endl;
    std::cout <<  "x3 Type is " << type_id_with_cvr<decltype(x3)>().pretty_name() << std::endl;
    std::cout   <<"x4 Type is "<<  type_id_with_cvr<decltype(x4)>().pretty_name() << std::endl;

    auto ax1 = 27;
    auto ax2(27);
    auto ax3={27};
    auto ax4{27};

    std::cout  << "ax1 Type is " << type_id_with_cvr<decltype(ax1)>().pretty_name() << std::endl;
    std::cout <<  "ax2 Type is " << type_id_with_cvr<decltype(ax2)>().pretty_name() << std::endl;
    std::cout <<  "ax3 Type is " << type_id_with_cvr<decltype(ax3)>().pretty_name() << std::endl;
    std::cout  <<"ax4 Type is "  <<type_id_with_cvr<decltype(ax4)>().pretty_name() << std::endl;

    return 0;
}
```

程序的输出如下:
使用 ```clang6.0```   编译。

```console {linenos=table}
x Type is int
cx Type is int const
crx Type is int const&
rx Type is int&

uref1 Type is int&
uref2 Type is int const&
uref3 Type is int&

name Type is char const [13]
arr1 Type is char const*
arr2 Type is char const (&) [13]

func1 Type is void (int, double)
func1 Type is void (*)(int, double)
func2 Type is void (&)(int, double)
lamdaFunc Type is main::$_0

x1 Type is int
x2 Type is int
x3 Type is int
x4 Type is int

ax1 Type is int
ax2 Type is int
ax3 Type is std::initializer_list int>
ax4 Type is int

```

两者的区别主要在于 ```std::initializer_list```  
例如:

```cpp {linenos=table}
//代码1
template<typename T>
void func(T param)
{
}
```

对于形如代码1的代码，是不可以这样调用的```func({1,2,3});```  

如果要实现上面的调用方式，需要进行如下的实现

```cpp {linenos=tables}
template <typename T>
void func(std::initailizer_list T> param);
```

总结:

  1. ```auto```  的类型推导和```template```  类似，区别在于```auto```  默认由花括号```{}```  的初始化代表一个 ```std::initializer_list```  ，而模板的类型推导不是这样的。
  2. 用在函数返回值的```auto```  实现的是模板类型推导，而不是auto类型推导。