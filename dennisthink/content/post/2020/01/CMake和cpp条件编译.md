---
title: CMake和Cpp条件编译(一)---CMake的条件编译
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
tags: ["cmake","cpp","code-note"]
comment: true
mathjax: false
---

# CMake和Cpp条件编译(一)---CMake的条件编译

在软件开发过程中,我们往往需要根据实际情况进行不同的编译,比如编译一个软件的Debug版本和Release版本,需要编译一个人软件的试用版和正式版等。在这样的情况下,条件编译就非常有用了,接来下我分为CMake的条件编译和CMake设置cpp的条件编译两部分来介绍。

## 1 CMake条件编译

在CMake中,我们可以通过调用message打印输出,来确定我们的选项是如何设置的。因为直接执行CMake ..输出的信息比较多,我们可以用同样的指令执行两遍来查看输出的效果。

### 1.1 CMake的define示例

1. CMakeLists.txt 文件
```cmake
cmake_minimum_required(VERSION 3.0)
project(CMakeDemo2)
if(DEFINED DEBUG_LOG)
    message("DEBUG_LOG is defined")
else()
    message("DEBUG_LOG is not defined")
endif()
```

2. `cmake ..`的输出效果

![define_default](https://www.dennisthink.com/image/2020/10/CMake的define示例_未设置DEBUG_LOG.jpg)

3. `cmake .. -DDEBUG_LOG=1`的输出效果

![define_DEBUG_LOG=1](https://www.dennisthink.com/image/2020/10/CMake的define示例_设置DEBUG_LOG的效果.jpg)

## 1.2 CMake的if示例

1. CMakeLists.txt文件
```cmake
cmake_minimum_required(VERSION 3.0)
project(CMakeDemo1)
option(DEBUG_LOG "Print log for debug version" ON)
if(DEBUG_LOG)
    message("DEBUG_LOG is on")
else()
    message("DEBUG LOG is off")
endif(DEBUG_LOG)
```

2. `cmake .. -DDEBUG_LOG=ON`的输出效果

![](https://www.dennisthink.com/image/2020/10/CMake的if示例DEBUG_LOG_ON.jpg)

3. `cmake .. -DDEBUG_LOG=OFF`的输出效果

![](https://www.dennisthink.com/image/2020/10/CMake的if示例DEBUG_LOG_OFF.jpg)

## 1.3 CMake中的数值比较

在CMake中,我们可以通过比较数值,来选择不同的操作,比如下面这个例子。数值比较我们使用的是`LESS`(小于)、`EQUAL`(等于)、`GREATER`(大于)

1. CMakeLists.txt的示例
```cmake
cmake_minimum_required(VERSION 3.0)
project(CMakeDemo3)
if(${PREV_VALUE} LESS ${CUR_VALUE})
    message("PREV_VALUE < CUR_VALUE ")
elseif(${PREV_VALUE} EQUAL ${CUR_VALUE})
    message("PREV_VALUE == CUR_VALUE")
elseif(${PREV_VALUE} GREATER ${CUR_VALUE})
    message("PREV_VALUE > CUR_VALUE")
else()
    message("PREV_VALUE unknown relation with CUR_VALUE")
endif()
```

2. `cmake ..`的输出

![默认输出](https://www.dennisthink.com/image/2020/10/CMake的数值比较之默认编译.jpg)

3. `cmake .. -DPREV_VALUE=1 -DCUR_VALUE=2`的输出(LESS)

![LESS输出](https://www.dennisthink.com/image/2020/10/CMake的数值比较_PREV_LESS_CUR.jpg)

4. `cmake .. -DPREV_VALUE=2 -DCUR_VALUE=2`的输出(EQUAL)
![](https://www.dennisthink.com/image/2020/10/CMake的数值比较之_EQUAL.jpg)

5. `cmake .. -DPREV_VALUE=2 -DCUR_VALUE=1`的输出(GREATER)
![](https://www.dennisthink.com/image/2020/10/CMake的数值比较之_GREATER.jpg)

## 1.4 CMake中的字符串比较

在CMake中,我们可以通过`STRLESS`(小于)、`STREQUAL`(等于)、`STRGREATER`(大于)来进行字符串的比较,从而确定不同的编译选择。

1. CMakeLists.txt的示例
```cmake
cmake_minimum_required(VERSION 3.0)
project(CMakeDemo4)
if(${PREV_VALUE} STRLESS ${CUR_VALUE})
    message("PREV_VALUE strless CUR_VALUE")
elseif(${PREV_VALUE} STREQUAL ${CUR_VALUE})
    message("PREV_VALUE strequal CUR_VALUE")
elseif(${PREV_VALUE} STRGREATER ${CUR_VALUE})
    message("PREV_VALUE strgreater CUR_VALUE")
else()
    message("PREV_VALUE unknown CUR_VALUE")
endif()
```

2. `cmake ..`的默认编译

![String_default](https://www.dennisthink.com/image/2020/10/CMake的String比较之默认情况.jpg)

3. `cmake .. -DPREV_VALUE=A -DCUR_VALUE=B`的输出(STRLESS)

![String_STRLESSS](https://www.dennisthink.com/image/2020/10/CMake的String比较之STRLESS.jpg)

4. `cmake .. -DPREV_VALUE=B -DCUR_VALUE=B`的输出结果(STREQUAL)

![](https://www.dennisthink.com/image/2020/10/CMake的String比较之STREQUAL.jpg)

5. `cmake .. -DPREV_VALUE=B -DCUR_VALUE=A`的输出结果(STRGREATER)
![](https://www.dennisthink.com/image/2020/10/CMake的String比较之STRGREATER.jpg)

以上就是对CMake的条件编译的简单介绍,如果有什么遗漏或者错误的地方,欢迎大家指出。