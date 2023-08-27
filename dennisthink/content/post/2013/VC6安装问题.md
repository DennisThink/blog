---
title: "VC6.0 安装问题"
date: 2013-05-11-T21:38:52+08:00
lastmod: 2013-05-11T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-csdn"]
comment: true
mathjax: false
---

错误信息:
Error spawning cl.exe 错误

今天安装 VC6.0 的时候出现了这个问题，后来在网上百度了一下，网友给出了答案，现在说说我的解决方案。

这个问题是因为 MSDEV 无法找到 cl.exe, 应该是以前装过 VC6.0 但是没有卸载完全引起的，可以通过修改环境变量来解决。

在用户的环境变量中增加

1，include 变量，它的值为

```console
C:/Program Files/Microsoft Visual Studio/VC98/INCLUDE;
C:/Program Files/Microsoft Visual Studio/VC98/MFC/INCLUDE;
C:/Program Files/Microsoft Visual Studio/VC98/ATL/INCLUDE;
```

其实就是系统头文件的位置

2，第二个环境变量 lib

```console
C:/Program Files/Microsoft Visual Studio/VC98/LIB;
C:/Program Files/Microsoft Visual Studio/VC98/MFC/LIB;
```
就是静态链接库的位置

3,source
```
C:/Program Files/Microsoft Visual Studio/VC98/MFC/SRC;
C:/Program Files/Microsoft Visual Studio/VC98/MFC/INCLUDE;
C:/Program Files/Microsoft Visual Studio/VC98/ATL/INCLUDE;
C:/Program Files/Microsoft Visual Studio/VC98/CRT/SRC;
```
是系统源文件的位置

4，path

```
C:/Program Files/Microsoft Visual Studio/Common/MSDev98/Bin;
C:/Program Files/Microsoft Visual Studio/VC98/BIN;
C:/Program Files/Microsoft Visual Studio/Common/TOOLS;
C:/Program Files/Microsoft Visual Studio/Common/TOOLS/WINNT;
```

所有的二进制可执行文件的位置

到此为止，所有的问题解决了，嘿嘿