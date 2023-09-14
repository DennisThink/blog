---
title: "Effective Morden C++ 读书笔记(3)"
date: 2017-08-20T21:38:52+08:00
lastmod: 2022-08-28T21:41:52+08:00
weight: 50
slug: note-effective-modern-cpp-learn-three
# you can close something for this content if you open it in config.toml.
comment: false
mathjax: false
---

# Item5 使用auto而不是确定的类型

代码链接:[github](https://github.com/DennisBookNote/EffectiveMordenCppNote/tree/master/code/Item5)

### 1使用auto必须初始化，否则会报错，而使用具体类型不会
```cpp {linenos=table}
int x ;
auto y;
auto z = 0;
```

对于上面的3行代码，代码编译的情况是:
1.```int x ;```没有正确初始化，编译器没有报错。
2.```auto y;```没有正确初始化，编译器报错了。错误消息:```error: declaration of ‘auto y’ has no initializer
  auto y;```
3.```auto z = 0;```正确初始化，编译器没有报错。
以上的三条语句表达的同样的意思，但是从代码的健壮性上来说，最后一条语句的健壮性最好，因此推荐使用```auto``` 来声明变量。

### 2 使用auto可以进行更短类型声明

```cpp {linenos=table}
class Widget{
public:
    explicit Widget(){};
    bool operator   (const Widget& rhs){
        return true;
    }
};

void autoLamda() {
    auto printFunc = [](const std::unique_ptr<Widget>& lhs,
                        const std::unique_ptr<Widget>& rhs)->bool {
                            return *lhs   * rhs;
                        };
    printFunc(std::make_unique<Widget>(),std::make_unique<Widget>());
}

std::function bool(const std::unique_ptr<Widget>& lhs,const std::unique_ptr <Widget>& rhs)> notAutoFunc = [](const std::unique_ptr<Widget>& lhs,
                        const std::unique_ptr<Widget>& rhs)->bool {
                            return *lhs   * rhs;
                        };

void notAutoLamda(){
    notAutoFunc(std::make_unique<Widget>(),std::make_unique<Widget>());
}
```

在上面的代码中，```autoLamda``` 中的 ```printFunc``` 和 ```notAutoFunc``` 声明了同样的函数类型的变量，但是使用```auto```的表达可以更为简洁，因此提倡使用 ```auto```。

### 3 在std容器中使用auto。

```cpp {linenos=table}
void AutoVector()
{
    std::vector<Widget> intVector;
    auto count = intVector.size();

    for(const auto& it:intVector){

    }
}

void NormalVector()
{
    std::vector<Widget> intVector;
    std::size_t count = intVector.size();

    for(std::vector<Widget>::iterator it = intVector.begin() ;
        it != intVector.end();
        ++it)
    {

    }
}
```
上面的两个函数较好的展示了使用```auto```和传统方式在对于std::vector进行操作时候的不同。可以看出，使用auto的代码更为简短，当```std::vector```内的变量类型发生变化的时候，auto的版本改动最少。

### 4 总结:

>  auto类型必须被初始化(依靠初始化进行类型的判断）。对于类型不匹配具有免疫力(例如我们经常会把 std::vector.size()的返回值声明为 int,实际类型是 std::size_t。可以更好的进行重构，需要输入的字符更少。