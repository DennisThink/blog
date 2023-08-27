---
title: 自定义顺序的std::map
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---


对于std::map，默认使用std::less作为比较函数，我们可以通过自定义比较函数，来实现其他排序的std::map。

以下是示例代码:

```cpp {linenos=table}
#include  <iostream>
#include  <map>
#include  <unordered_map>
#include  <vector>

class CLessCmp
{
public:
    bool operator()(int x,int y)
    {
        return x < y;
    }
};

class CGreatCmp
{
public:
    bool operator() (int x,int y)
    {
        return x > y;
    }
};

int main(int argc,char * argv[])
{

    std::map<int,int> defaultMap;

    std::map<int,int,CLessCmp> lessMap;

    std::map<int,int,CGreatCmp> greatMap;

    std::vector<int> orgVector={1,4,2,3,8,9,6};


    for(const auto & item:orgVector)
    {
        lessMap.insert(std::pair<int,int>(item,item));
        greatMap.insert(std::pair<int,int>(item,item));
        defaultMap.insert(std::pair<int,int>(item,item));
    }

    std::cout << "Default Map  " << std::endl;
    for(const auto& item:defaultMap)
    {
        std::cout<<item.first<<"\t";
    }
    std::cout<<std::endl;


    //lessMap
    std::cout << "Less Map   "  <<std::endl;
    for(const auto& item:lessMap)
    {
        std::cout << item.first << "\t";
    }
    std::cout<<  std::endl;
    std::cout << "great Map  " << std::endl;
    for(const auto& item:greatMap)
    {
        std::cout << item.first << "\t";
    }
    std::cout<<std::endl;

    return 0;
}
``` 

程序的输出如下:

![](https://www.dennisthink.com/image/2019/01/map_learn.png)

可以看出：
默认的std::map的对于key的排序是从小到大。
自定义排序算法以后，输出改为从大到小。