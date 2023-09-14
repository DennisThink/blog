---
title: "golang编程之我见"
date: 2017-11-23T21:38:52+08:00
lastmod: 2017-11-23T21:41:52+08:00
slug: my_option_about_go_program_language
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-csdn"]
comment: true
mathjax: false
---

# golang编程之我见

学习了两个月的golang，语法算是基本掌握了，从一个C++程序员的角度，提出自己的几个看法吧。

### 1，没有一个好的包管理工具。
我在公司用的是glide的包管理，这个工具的好处就是可以根据git提交的hash值选择不同的版本。
问题有2个:
1. 每次执行完glide init以后，需要在glide.yaml中手动修改版本号，否则可能会有API不兼容的问题。
2. 因为有部分库需要从google官方下载，需要fan qiang，每次都比较慢。
不知道为什么golang不直接像python一样，在工具里直接自带包管理。或者像C++一样，直接由程序员来做包管理。

### 2.没有面向对象常用的继承、封装、多态。
golang作为一个在21世纪出现的编程语言，提供了interface和struct，却没有提供继承、封装和多态。
其继承关系由接口来表示，例如

```golang
//golang 
type IParent interface {
    func Print()
}

type Son struct{
}

func (s *Son)Print() {
	fmt.Println("i am son")
}
```
示例代码中的Son即实现了IParent接口，不同于Python，Java，C++等明确的接口实现关系，golang的接口实现是隐式说明的。
如果Son和IParent在不同的源文件中，甚至在不同的库中，让程序员找出这个对应关系是对程序员不友好的。

### 3. 语言特性未使用到
作为一个服务器端开发人员，因为我们的系统采用微服务架构，所以每个服务程序的代码都非常的简短。但是在这个简短的代码里，我从来没有发现使用过 channel和go routine的代码。我不知道是这两个特性比较难，所以不用，还是因为我们的程序太简单所以用不到。作为一个C++程序员的时候，表示用不到template感觉就不像在写C++。