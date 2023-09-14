---
title: "hides overloaded virtual function 警告"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
slug: hide-overload-virtual-function-warning
comment: true
mathjax: false
---

# hides overloaded virtual function 警告

在使用clang编译[mysql-connector-cpp](https://github.com/mysql/mysql-connector-cpp)报告了一条警告```hides overloaded virtual function```，(在开启所有警告以及额外警告的前提下)。

在StackOverflow上可以看到一条讨论的链接[stackOverflow](https://stackoverflow.com/questions/18515183/c-overloaded-virtual-function-warning-by-clang)

这条警告复现的一个简单的示例如下:

```cpp {linenos=table}
#include  <iostream>

class Base
{
public:
    virtual int Add(int x)
    {
        return x+1;
    }
};

class Drived:public Base
{
public:
    virtual int Add(int x,int y)
    {
        return x+y;
    }
};

int main(int argc,char* argv[])
{
    std::cout << argc << "\t"<<  argv[0] << std::endl;
    return 0;
}
``` 

使用g++ 7.3.0编译，不会报警告失败。


>  g++ -Wall -Wextra -Werror Test3.cpp


![](https://www.dennisthink.com/image/2019/01/g.png)

使用clang++ 6.0.0报警告


>  clang++ -Wall -Wextra -Werror ./Test3.cpp


![](https://www.dennisthink.com/image/2019/01/clang.png)

这个警告的意思主要是防止不小心用重载覆盖了重写。本意是想重写，结果重载了。
比如下面这样的情况:

```cpp {linenos=table}
struct chart; // let's pretend this exists
struct Base
{
    virtual void* get(char* e);
};

struct Derived: public Base {
    virtual void* get(chart* e); // typo, we wanted to override the same function
};
``` 

在Drived的类中加入 using Base::Add;可以去除这条警告。