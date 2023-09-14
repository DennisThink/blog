---
title: "《Effective Modern C++》读书笔记(4)"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
slug: note-effective-modern-cpp-learn-four
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---
# Item 7区分使用 () 和{}创建对象    

### 1. 基本数据类型

```cpp {linenos=table}
void BaseTypeUse()
{
    int x(0);
    int y=0;
    int z{0};
    int z2={0};
}
``` 

### 2.标准库的数据类型

```cpp {linenos=table}
void VectorUse()
{
    std::vector<int> v{1,3,4,5};
    std::map<int,std::string> m{
        {1,"ok"},
        {2,"over"}
    };
}
``` 

### 3.类的成员变量

```cpp {linenos=table}
class Widget
{

private:
    int x{0};
    int y=0;
    //int z(0);//Compiler Error
};
``` 

### 4.没有使用```std::initializer_list```的类

```cpp {linenos=table}
class WidgetBase
{
public:
    WidgetBase()
    {
        std::cout<<  __LINE__  <<"Default No Param constructor" << std::endl;
    }

    WidgetBase(int x,double y)
    {
        std::cout << __LINE__ << "One Int One Double Constructor"<<  std::endl;
    }

    WidgetBase(int x,bool y)
    {
        std::cout << __LINE__  <<"one Int One bool Contructor" << std::endl;
    }

    WidgetBase(const WidgetBase& other){
        std::cout << "Copy Constructor" << std::endl;
    }

    WidgetBase operator = (const WidgetBase& other){
        std::cout << "Equal Constructor" << std::endl;
    }

private:
    int x{0};
    int y=0;
    //int z(0);//Compiler Error
};


WidgetBase w9(10,true);
WidgetBase w10{10,true};
WidgetBase w11(10,5.0);
WidgetBase w12{10,5.0};
``` 

### 5.使用了```std::initializer_list```的类

```cpp {linenos=table}
class Widget
{
public:
    Widget()
    {
        std::cout << __LINE__ << "Default No Param constructor"<<  std::endl;
    }

    Widget(int x)
    {
        std::cout << __LINE__ << "One Int Param constructor"  <<std::endl;
    }

    Widget(int x,double y)
    {
        std::cout << __LINE__ << "One Int One Double Constructor" << std::endl;
    }

    Widget(int x,bool y)
    {
        std::cout << __LINE__<<  "one Int One bool Contructor" << std::endl;
    }

    Widget(std::initializer_list long double> il)
    {
        std::cout << __LINE__ << "std::initializer long double Constructor" << std::endl;
    }

    Widget(const Widget& other){
        std::cout << "Copy Constructor" << std::endl;
    }

    Widget operator = (const Widget& other){
        std::cout << "Equal Constructor" << std::endl;
    }

    operator float () const{
        std::cout << "operator float"  <<std::endl;
        return 0.0;
    }

    private:
    int x{0};
    int y=0;
    //int z(0);//Compiler Error
};

Widget w;
Widget w2=w;
w=w2;
Widget w3{};
Widget w4();

Widget w5(10,true);
Widget w6{10,true};
Widget w7(10,5.0);
Widget w8{10,5.0};

Widget w81{10,5.0};
Widget w82{w81};
Widget w83(w82);
``` 

### 总结


  对于基本类型的初始化,```()``` 和 ```{}```的区别不大.
  使用```{}``` 能更方便的对标准模板库进行初始化.
  类的成员变量可以使用 ```{}``` 和 ```=``` 进行初始化,但是不能使用 ```()```.
  对于不含有```std::initializer_list```为参数的构造函数来说,使用```()```和```{}```构造对象的方式基本相同.
  对于含有```std::initializer_list```为参数的构造函数的对象来说,```()```使用默认的构造函数,```{}```优先使用 ```std::initializer_list``` 为参数的构造函数.