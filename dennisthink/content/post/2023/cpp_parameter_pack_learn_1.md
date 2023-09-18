---
title: "C++可变参数模板"
date: 2023-09-18T21:38:52+08:00
lastmod: 2017-09-18T21:41:52+08:00
slug: 
weight: 90

# you can close something for this content if you open it in config.toml.
comment: false
mathjax: false
---
c++ 可变参数模板----从求和说起

## 1. 两个参数的整数求和函数

```cpp
int sum(int x,int y)
{
    return x+y;
}
```

## 2. 两个参数的浮点数求和函数
```cpp
int sum(float x,float y)
{
    return x+y;
}
```


模板实现:
```cpp
template<typename T>
T sum(T x, T y)
{
    return x+y;
}
```

## 3. 一个整数一个浮点数的求和函数
```cpp
int sum(int x,float y)
{
    return x+y;
}

int sum(float x,int y)
{
    return x+y;
}
```

模板实现:
```cpp
template<typename T,typename U>
T sum(T x,U y)
{
    return x+y;
}
```

## 4. 多个整数求和

```cpp
int sum(int x,int y ,int z)
{
    return x+y+z;
}

int sum(int x,int y,int z,int a)
{
    return x+y+z+a;
}
....
```

## 5. 多个浮点数求和

```cpp
float sum(float x,float y ,float z)
{
    return x+y+z;
}

float sum(float x,float y,float z,float a)
{
    return x+y+z+a;
}
....
```
模板方法:
```cpp

template<typename T>
T sum(T x,T args...)
{
    return x+sum(args...); 
}

template<typename T>
T sum(T x)
{
    return x;
}
```

### 6,多个整数和浮点数混合求和

