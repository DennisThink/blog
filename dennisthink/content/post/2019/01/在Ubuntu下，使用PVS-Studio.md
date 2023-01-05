---
title: 在Ubuntu下，使用PVS-Studio.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

#在Ubuntu下，使用PVS-Studio

简介:
PVS-Studio是一个C++，C#代码质量分析工具,因为我不用C#，所以这里介绍一下它在Linux下的分析C++代码质量的功能。官方网址在:https://www.viva64.com.

## 1.安装。    

安装的主要过程参考 https://www.viva64.com/en/m/0039/。

在Ubuntu控制台输入以下命令：

```
  wget -q -O - http://files.viva64.com/etc/pubkey.txt | \
    sudo apt-key add - br />
  sudo wget -O /etc/apt/sources.list.d/viva64.list \
    http://files.viva64.com/etc/viva64.list br />
  sudo apt-get update br />
  sudo apt-get install pvs-studio
 ```


稍等一会儿就安装好了，
检查是否安装完成。

```
  pvs-studio --version
 ```


出现下图表示安装完成

 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/PVS-Version.png" alt="PVS_VERSION" />

## 2.运行官方示例    

前提:
需要提前安装好cmake以及c++编译器。

官方示例的网址在:

https://github.com/viva64/pvs-studio-cmake-examples。

下载zip包，并且解压。
在控制台中进入解压后的目录，创建编译目录并进入。

```
  mkdir PVS
  cd PVS
 ```


对第一个例子进行编译。

```
  cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=On ../example-1
 ```


控制台输出:
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/console_error.png" alt="example_console_error" />

代码错误位置(打开example-1的main.cpp):
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/example_error.png" alt="example_code_error" />

## 3.在自己的代码中使用    

 ol>
 li>下载一个没有使用PVS-Studio的代码
以我的Calculater为例 https://github.com/DennisCoder1024/Calculater/releases。 /p> /li>
 li> p>解压进入到目录，在source目录下有CMakeLists.txt /p> /li>
 li> p>创建目录，编译原有项目。 /p> /li>
 /ol>

```
   p>mkdir Build br />
  cd Build br />
  cmake .. br />
  make -j
 ```


 ol start="4">
 li>将 code>pvs-example ```的 code>PVS-Studio.cmake ```拷贝到
解压目录。 /p> /li>
 li> p>添加PVS-Studio编译需要的相关代码,。
在CMakeLists.txt中，
找到 code>add_executable(calcu ${SOURCE_FILE}) ```
在后面添加 /p> /li>
 /ol>

```
if(PVS_STUDIO)
    include(../PVS-Studio.cmake)
    pvs_studio_add_target(TARGET example1.analyze ALL
                      OUTPUT FORMAT errorfile
                      ANALYZE ${PROJECT}
                      MODE GA:1,2 OP
                      LOG target.err)
endif()
 ``` /pre>

 p> code>${PROJECT} ```的内容要替换为 code>add_executable ```的内容即 code>calcu ```。
修改好的内容

```
if(PVS_STUDIO)
    include(../PVS-Studio.cmake)
    pvs_studio_add_target(TARGET example1.analyze ALL
                      OUTPUT FORMAT errorfile
                      ANALYZE calcu
                      MODE GA:1,2 OP
                      LOG target.err)
endif()
 ``` /pre>

 ol start="5">
 li>修改源代码
在每个源文件的前面，加入以下两行 /li>
 /ol>

```
// This is an open source non-commercial project. Dear PVS-Studio, please check it.
// PVS-Studio Static Code Analyzer for C, C++ and C#: http://www.viva64.com
 ``` /pre>

 ol start="6">
 li>使用下面的命令编译代码。 /li>
 /ol>

```
  cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=On -DPVS_STUDIO=1 .. br />
  make
 ```


 ol start="7">
 li>错误输出
可以看到检查出了错误，如下图所示：
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/calculator_error.png" alt="error" /> /li>
 /ol>

代码位置在:
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/error_code.png" alt="error_code" />