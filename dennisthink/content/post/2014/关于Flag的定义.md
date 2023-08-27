---
title: "关于Flag的定义"
date: 2014-03-26T21:38:52+08:00
lastmod: 2014-03-26T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-cnblogs"]
comment: true
mathjax: false
---
最近在维护项目的代码，发现了由于Flag不一致导致的很多问题，现在对这一问题总结。

1. flag分为两种，可以组合的和不可以组合的。可以组合的flag适合用每一位表示一个含义。不适合组合的flag适合用枚举类型。

例如：窗口按钮选项适合用位来表示(最大化|最小化|关闭)

窗口类型(模式对话框VS非模式对话框)适合用枚举。

2. 修改代码的时候，由于无法弄清每个flag的关系，改一个bug总是要依靠之前的数据。这样修改起来就会一个地方改了其他地方跟着动。

3. 建议对于不同的情况直接编写不同的函数。由于情况组合很复杂的，尽量在编写代码的过程中进行重构，以此防止代码腐化。

 