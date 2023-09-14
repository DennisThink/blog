---
title: 《Effective Modern C++》读书笔记(5)"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
slug: note-effective-modern-cpp-learn-five
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---
# item8 使用nullptr代替0和NULL

## 1. 使用nullptr能更好的进行函数重载 
函数声明:

```cpp {linenos=tables} 
void PointerOverLoad(int x)
{
    std::cout << "int Function"<<  x  <<std::endl;
}

void PointerOverLoad(bool x)
{
    std::cout<<  "bool function" << x  <<std::endl;
}

void PointerOverLoad(void * x)
{
    std::cout << "void pointer function"<<  x << std::endl;
}
``` 

函数调用:

``` 
PointerOverLoad(0); //call intFunction
PointerOverLoad(nullptr);//call void pointer function
PointerOverLoad(NULL);// compiler failed
``` 

出错信息:

``` 
error: call of overloaded ‘PointerOverLoad(NULL)’ is ambiguous
  PointerOverLoad(NULL);
``` 

总结:

nullptr可以和0进行区分,实现不同的函数调用.
  PointerOverLoad(NULL);编译出错,防止了错误的函数函数调用.

### 2. 返回值的比较方便区分不同的返回值类型
函数声明:

```cpp
std::shared_ptr int> GetIntPointer()
{
    return std::make_shared<int>(0);
}

``` 

函数调用:

```cpp
auto result = GetIntPointer();
if( 0 == result)
{
    std::cout << "GetIntPointer failed" << std::endl;
}

auto result2 = GetIntPointer();
if( nullptr == result2)
{
    std::cout  <<"GetIntPointer failed"  <<std::endl;
}
``` 

总结:


  使用 if(0 == result)的比较,不太容易区分返回值的类型到底是什么,一般来说会默认是int.
  使用 if(nullptr == result2)的比较,非常容易让人知道result2是一个指针类型.



### 3. 通过模板函数避免函数错误重载

函数声明:

```cpp 
int IntFunc(std::shared_ptr int> spi)
{
    std::cout << "Int Func" << spi << std::endl;
    return 0;
}

double IntFuncUniq(std::unique_ptr int> upi)
{
    std::cout << "Unique ptr" << upi.get()<<  std::endl;
    return 0.0;
}

bool IntFuncPtr(int * ptr)
{
    std::cout << "IntFuncPtr" << ptr << std::endl;
    return false;
}
``` 

无法区分错误的函数调用:

```cpp 
std::mutex f1m;
std::mutex f2m;
std::mutex f3m;
using MuxGuard = std::lock_guard<std::mutex>;
{
    MuxGuard g(f1m);
    auto result = IntFunc(0);
    std::cout << result << std::endl;
}


{
    MuxGuard g(f2m);
    auto result = IntFuncUniq(NULL);
    std::cout << result<<  std::endl;
}


{
    MuxGuard g(f3m);
    auto result = IntFuncPtr(nullptr);
    std::cout<<  result<<  std::endl;
}
``` 

使用模板类型区分错误调用:

```cpp
template <typename FuncType,
         typename MuxType,
         typename PtrType>
auto lockAndCall(FuncType func,
                 MuxType&  mutx,
                 PtrType  ptr)
{
    using MuxGuard = std::lock_guard<MuxType>;
    MuxGuard g(mutx);
    return func(ptr);
}
``` 

```cpp 
auto result1 = lockAndCall(IntFunc,f1m,0);

auto result2 = lockAndCall(IntFuncUniq,f2m,NULL);

auto result3 = lockAndCall(IntFuncPtr,f3m,nullptr);
``` 

编译结果:

```console
error: ‘void result1’ has incomplete type
  auto result1 = lockAndCall(IntFunc,f1m,0);

error: ‘void result2’ has incomplete type
  auto result2 = lockAndCall(IntFuncUniq,f2m,NULL);
``` 

总结:


  使用模板函数可以去除重复代码,并且防止错误重载.


总结:


  总是使用nullptr代替0和NULL .
  避免int和指针类型的重载.