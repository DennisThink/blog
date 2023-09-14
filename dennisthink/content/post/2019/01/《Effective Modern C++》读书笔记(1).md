---
title: "《Effective Modern C++》读书笔记(1)"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
slug: note-effective-modern-cpp-learn-one
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Item 1 尽量使用const和inline而不用#define

目前代码里用到 #define 需要替换的地方主要有两处

## 1. 常量定义

例如我们需要定义 π 为3.14,有两种办法
(1) 使用宏

```cpp {linenos=table}
#define MACRO_PI 3.14
```

(2) 使用const常量

```cpp {linenos=table}
const int CONST_PI=3.14
```

### 2. 函数声明

(1) 使用宏

```cpp {linenos=table}
#define MACRO_MAX(a,b) ((a)>(b)?(a):(b))
```

(2) 用函数模板

```cpp {linenos=table}
template <typename T>
T mymax(T num1,T num2)
{
   return num1>num2 ? num1 : num2;
}
```

### 3.两者的区别

代码示例如下

```cpp {linenos=table}
#include  <iostream>

#define MACRO_PI 3.14

const double CONST_PI=3.14;

double GetCirclrCircumferenceMacro(const double radius)
{
    return 2*MACRO_PI*radius;
}

double GetCirclrCircumferenceConst(const double radius)
{
    return 2*CONST_PI*radius;
}

void PrintPi(char pi)
{
    int value = pi;
    std::cout  value  std::endl;
}

#define MACRO_MAX(a,b) ((a)>(b)?(a):(b))

template typename T>
T mymax(T num1,T num2)
{
   return num1>num2 ? num1 : num2;
}

const int MAX_VALUE=500;
#define MACRO_MAX_VALUE 500

int main(int argc,char * argv[])
{
    std::cout<<  "argc  "  <<argc  << " argv " << argv[0]<<  std::endl;
    std::cout<<  "GetCirsumByMacro " << GetCirclrCircumferenceMacro(2.0)  <<std::endl;
    std::cout << "GetCirsumByConst " << GetCirclrCircumferenceConst(2.0) << std::endl;

    int a = 2;
    int b = 4;
    double c = 3.0;
    std::cout << MACRO_MAX(c+c,MACRO_MAX(a+a,b+b)) << std::endl;
    std::cout << mymax(c+c,mymax(a+a,b+b))<<  std::endl;

    char value = MAX_VALUE;
    char value2 = MACRO_MAX_VALUE;

    std::cout << value << std::endl;
    std::cout << value2 << std::endl;  
    return 0;
}
```

编译命令为:

```console
g++ -Wextra ConstVsDefine.cpp 
```

编译的错误提示如下:![item1-compile-result](https://www.dennisthink.com/image/2019/01/item1_compile_result.png)

注意三个地方:
1. 我们注意到对于宏的地方```#define MACRO_MAX(a,b) ((a)>(b)?(a):(b))```，需要包含两层括号，才能比较好的完成嵌套。
2. 图中第一个红色框
我们模板对于double和int因为是不同类型的比较，所以报错了。较为明显的反应了不同类型的问题。而宏没有报错，这样如果最后我们发现结果不一样，很难想到是类型的原因，掩盖了错误。
3. 图中第二个红色框
宏的报错使用了两条信息，而const变量只使用了一条。