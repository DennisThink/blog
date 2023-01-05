---
title: C++迭代器失效
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# C++迭代器失效

今天去面试,面试官问到了迭代器失效的问题,我对这个问题不了解。结束面试以后,觉得应该看看在这方面有哪些问题,所以就有了这篇文章。

## 1、讨论容器失效的文章

在网上找到了一些讨论容器失效的文章,我觉得还不错,放个列表在下面。

[StackOverflow-Iterator-invalidation-rules](https://stackoverflow.com/questions/6438086/iterator-invalidation-rules)

[STL容器迭代器失效情况分析、总结](https://ivanzz1001.github.io/records/post/cplusplus/2018/03/14/cpluscplus_stl_iterator)

[迭代器失效的几种情况](https://blog.csdn.net/baidu_37964071/article/details/81409272)

## 2、我理解的迭代器

我把vector和list的迭代器的部分源码看了一下,我的理解是迭代器是指向容器元素的指针。迭代器通过对容器实际结构的封装,在上层的代码调用层面,完成了操作的统一。

以vector和list举例,vector的实现一般是以数组的形式,所以vector的iterator保存了数组元素的内存地址,所以在移动到上一个或者下一个元素的时候,直接就是偏移了一个元素大小的位置。而对于list这样通过双向链表来实现的容器,在迭代器进行移动的时候,就是调用Prev()和Next()函数了。

## 3、分析迭代器失效

迭代器失效的本质就是迭代器指向的位置的元素发生了移动,而迭代器本身没有移动,在这样的情况下,迭代器就失效了。以vector{1,2,3,4,5}举例,假设有一个迭代器itA指向2,如果在1之后插入一个元素1,那么itA有可能指向新插入的1,此时我们就认为itA这个迭代器失效了。如果你保存了之前的end迭代器,那么在添加元素以后,该迭代器也会失效。对于list来说,内部是通过双向链表来实现的,那么在链表的中间插入元素,并不会影响end迭代器。

通过这样的方法,可以分析出什么样的操作会影响迭代器,并且可以知道影响那些迭代器。