---
title: 使用using代替typedef.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Item9 使用 ```using ```代替 ```typedef ```    

### 1.类型声明    

```cpp
//Type declication
typedef std::unique_ptr<std::unordered_map<std::string,Wiget>> UPtrMapSW_T;
using UPtrMapSW_U = std::unique_ptr<std::unordered_map<std::string,Wiget>>;
```

### 2.函数声明    

```cpp
typedef void (*FP_T)(int,const std::string&);
using FP_U=void(*)(int,const std::string&);
```

### 3.标准模板库    

```cpp
template<typename T>
using MyAllocList_U = std::list<T>;

void UseMyAllocList_U()
{
    MyAllocList_U<Wiget> wList;
}

template<typename T>
struct MyAllocList_T{
    typedef std::list<T> type;
};

void UseMyAllocList_T()
{
    MyAllocList_T<Wiget>::type wList;
}
```

### 4. C++11的一些用法    

模仿C++14的声明:

```cpp
template<typename T>
using my_remove_const_t = typename std::remove_const<T>::type;

template<typename T>
using my_remove_reference_t = typename std::remove_reference<T>::type;

template<typename T>
using my_add_lvalue_reference_t =typename std::add_lvalue_reference<T>::type;
```

下面是函数调用:

```cpp
    //C++11
    {
        std::cout  "C++11 Begin"  std::endl;
        Wiget w;
        const Wiget cw;
        Wiget& wr = w;
        auto wRemoveRef = (std::remove_reference<Wiget>::type)(wr);
        auto wRemoveConst = (std::remove_const<Wiget>::type)(cw);
        auto wrAuto = wr;
        using boost::typeindex::type_id_with_cvr;
        std::cout<<type_id_with_cvr<decltype(wrAuto)>().pretty_name() << std::endl;
        std::cout << "Org:" << type_id_with_cvr<decltype(wr)>().pretty_name()  <<"    RemoveRef:" << type_id_with_cvr<decltype(wRemoveRef)>().pretty_name() << std::endl;
        std::cout << "Org:" << type_id_with_cvr<decltype(cw)>().pretty_name()  <<"    RemoveConst:" << type_id_with_cvr<decltype(wRemoveConst)>().pretty_name()  <<std::endl; 
        std::cout << "Org:"<<  type_id_with_cvr<decltype(cw)>().pretty_name() <<"    AddRef:"  << type_id_with_cvr<decltype((std::add_lvalue_reference Wiget>::type)(cw))>().pretty_name() << std::endl;
    }


    //C++14
    {
        std::cout << "C++14 Begin"  <<std::endl;
        Wiget w;
        const Wiget cw;
        Wiget& wr = w;
        auto wRemoveRef = (std::remove_reference_t<Wiget>)(wr);
        auto wRemoveConst = (std::remove_const_t<Wiget>)(cw);
        auto wrAuto = wr;
        using boost::typeindex::type_id_with_cvr;
        std::cout << type_id_with_cvr<decltype(wrAuto)>().pretty_name() << std::endl;
        std::cout<<  "Org:" << type_id_with_cvr<decltype(wr)>().pretty_name()<<  "    RemoveRef:" << type_id_with_cvr<decltype(wRemoveRef)>().pretty_name() << std::endl;
        std::cout  <<"Org:" << type_id_with_cvr<decltype(cw)>().pretty_name()  <<"    RemoveConst:" << type_id_with_cvr<decltype(wRemoveConst)>().pretty_name() << std::endl; 
        std::cout  <<"Org:"<<  type_id_with_cvr<decltype(cw)>().pretty_name()  <<"    AddRef:" <<  type_id_with_cvr<decltype((std::add_lvalue_reference_t <Wiget>)(cw))>().pretty_name() << std::endl;
    }

    //C++11 as 14
    {

        std::cout<<  "my C++14 Begin" << std::endl;
        Wiget w;
        const Wiget cw;
        Wiget& wr = w;
        auto wRemoveRef = (my_remove_reference_t<Wiget>)(wr);
        auto wRemoveConst = (my_remove_const_t<Wiget>)(cw);
        auto wrAuto = wr;
        using boost::typeindex::type_id_with_cvr;
        std::cout << type_id_with_cvr <decltype(wrAuto)>().pretty_name()  <<std::endl;
        std::cout << "Org:"  type_id_with_cvr<decltype(wr)>().pretty_name() << "    RemoveRef:" << type_id_with_cvr<decltype(wRemoveRef)>().pretty_name()  std::endl;
        std::cout << "Org:" << type_id_with_cvr<decltype(cw)>().pretty_name()  <<"    RemoveConst:"  <<type_id_with_cvr<decltype(wRemoveConst)>().pretty_name()  <<std::endl; 
        std::cout << "Org:" << type_id_with_cvr<decltype(cw)>().pretty_name()  "    AddRef:" <<  type_id_with_cvr<decltype((my_add_lvalue_reference_t Wiget>)(cw))>().pretty_name() << std::endl;
    }
```

总结:


1. ```typedef``` 不支持模板化,但是 ```using ``` 支持 参照 C++11的一些用法.
2.  ```using ```的模板类型,避免了 ```type``` 前缀,但是比 ```typedef```多使用了 ```typename ```.参照 C++11的一些用法
3. C++14比C++11提供了更为方便的类型转换.

