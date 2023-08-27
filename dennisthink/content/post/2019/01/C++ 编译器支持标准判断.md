---
title: "C++ 编译器支持标准判断"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
weight: 50
url: /blog/note/cpp-compiler-support-standard-verify/
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---


C++ 主要的历史版本有 
* C++98(1.0)
* C++03(TR1)
* C++11
* C++14
* C++17
* C++20.

1. 在GCC++和clang++编译器下，可以使用下面的代码进行判断。

```cpp {linenos=table}
#include  <iostream>
int main(int argc,char * argv[])
{
#ifdef __cplusplus
    std::cout << __cplusplus << std::endl;
#else
    std::cout << "No __cplusplus" << std::endl;
#endif 
    return 0;
}
```

在编译的时候，要指定-std=c++XX等，下图是在 Ubuntu 18.04使用g++和clang的编译情况。

系统信息:![system_info2.png](https://www.dennisthink.com/image/2019/01/system_info2.png)
g++编译：![g_result.png](https://www.dennisthink.com/image/2019/01/g_result.png)


clang++编译:![clang_result.png](https://www.dennisthink.com/image/2019/01/clang_result.png)

2. 在Windows平台的VS上要使用 其他的判断方式。