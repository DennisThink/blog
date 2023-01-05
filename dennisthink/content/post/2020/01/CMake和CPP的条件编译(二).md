---
title: CMake和cpp的条件编译(二)---通过CMake对cpp的条件编译进行设置
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# CMake和cpp的条件编译(二)---通过CMake对cpp的条件编译进行设置

在cpp中主要有以下几种设置条件编译的方式。

(1) `#ifdefine {MACRO}`或者`#ifndef {MACRO}`
通过定义了或者没有定义某个宏来开启或者关闭对应代码的编译。

(2) `#if {MACRO}`
通过某个宏的值为true(非0)或者false(0)来开启或者关闭对应代码的编译

(3) `#if defined({MACRO1}) && defined({MACRO2}`或者`#if defined({MACRO1}) || defined({MACRO2})`

通过某些宏的与/或条件,通过多个宏来开启或者关闭对应代码的编译,具体的使用看示例代码。在CPP中共有
DEFINE_CONDITION、UN_DEFINE_CONDITION、DEBUG_LOG、DEBUG_LOG_DEFINED、DEFINE_FIRST、DEFINE_SECOND、DEFINE_THIRD、DEFINE_FOURTH等,对于每一个选项在CMake中都通过在其后加SWITCH作为if条件来开启对应的宏。下面就对相关的代码和示例做说明。

1. CMake的代码示例

```c
cmake_minimum_required(VERSION 3.0)
project(CMakeCppDefineLearn)
if(DEFINED DEFINE_CONDITION_SWITCH)
    message("DEFINE_CONDITION is defined")
    add_definitions(-DDEFINE_CONDITION)
else()
    message("DEFINE_CONDITION is not defined")
endif()

if(DEFINED UN_DEFINE_CONDITION_SWITCH)
    message("UN_DEFINED_CONDITION is defined")
    add_definitions(-DUN_DEFINE_CONDITION)
else()
    message("UN_DEFINED_CONDITION is not defined")
endif()

if(DEBUG_LOG_SWITCH)
    message("DEBUG_LOG is [on] / [not zero]")
    add_definitions(-DDEBUG_LOG=1)
else()
    message("DEBUG_LOG is [off] / [zero] ")
    add_definitions(-DDEBUG_LOG=0)
endif()

if(DEBUG_LOG_DEFINED_SWITCH)
    message("DEBUG_LOG_DEFINED is defined")
    add_definitions(-DDEBUG_LOG_DEFINED)
else()
    message("DEBUG_LOG_DEFINED is not defined")
endif()

if(DEFINE_FIRST_SWITCH)
    message("DEFINE_FIRST is defined")
    add_definitions(-DDEFINE_FIRST)
else()
    message("DEFINE_FIRST is not defined")
endif()

if(DEFINE_SECOND_SWITCH)
    message("DEFINE_SECOND is defined")
    add_definitions(-DDEFINE_SECOND)
else()
    message("DEFINE_SECOND is not defined")
endif()

if(DEFINE_THIRD_SWITCH)
    message("DEFINE_THIRD is defined")
    add_definitions(-DDEFINE_THIRD)
else()
    message("DEFINE_THIRD is not defined")
endif()

if(DEFINE_FOURTH_SWITCH)
    message("DEFINE_FOURTH is defined")
    add_definitions(-DDEFINE_FOURTH)
else()
    message("DEFINE_FOURTH is not defined")
endif()


SET(SRC_FILE ./condition_compile_main.cpp)
add_executable(ConditionMain ${SRC_FILE})
```

2. 对应的cpp的代码示例

```cpp
/**
 * @file condition_compile_main.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief cpp 条件编译示例
 * @version 0.1
 * @date 2020-10-17
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>

int main(int argc, char* argv[])
{

#ifdef DEFINE_CONDITION
    std::cout<<"DEFINE_CONDITION is defined"<<std::endl;
#else
    std::cout<<"DEFINE_CONDITION is not defined"<<std::endl;
#endif

#ifndef UN_DEFINE_CONDITION
    std::cout<<"UN_DEFINE_CONDITION is not defined"<<std::endl;
#else
    std::cout<<"UN_DEFINE_CONDITION is defined"<<std::endl;
#endif

#if DEBUG_LOG
    std::cout<<"DEBUG_LOG is true([on]/[not zero]) "<<std::endl;
#else
    std::cout<<"DEBUG_LOG is false([off]/[zero]) "<<std::endl;
#endif

#if defined(DEBUG_LOG_DEFINED)
    std::cout<<"DEBUG_LOG_DEFINED is defined"<<std::endl;
#else
    std::cout<<"DEBUG_LOG_DEFINED is not defined"<<std::endl;
#endif

#if defined(DEFINE_FIRST) && defined(DEFINE_SECOND)
    std::cout<<"DEFINE_FIRST and DEFINE_SECOND is defined at same time"<<std::endl;
#else
    std::cout<<"DEFINE_FIRST and DEFINE_SECOND is not defined at same time"<<std::endl;    
#endif

#if defined(DEFINE_THIRD) || defined(DEFINE_FOURTH)
    std::cout<<"DEFINE_THIRD and DEFINE_FOURTH is define at lest one"<<std::endl;
#else
    std::cout<<"DEFINE_THIRD and DEFINE_FOURTH was not defined either"<<std::endl;
#endif

return 0;
}
```

3. DEFINE_CONDITION宏相关

(1) DEFINE_CONDITION定义了
CMake条件编译指令`cmake .. -DDEFINE_CONDITION_SWTICH=ON`
程序运行结果
![DEFINE_CONDITION_GOOD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINED_CONDITION_GOOD.jpg)

(2) DEFINE_CONDITION未定义
CMake条件编译指令`cmake .. -DDEFINE_CONDITION_SWTICH=OFF`
程序运行结果
![DEFINE_CONDITION_BAD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINED_CONDITION_BAD.jpg)


3. UN_DEFINE_CONDITION宏相关

(1) UN_DEFINE_CONDITION定义了
CMake条件编译指令`cmake .. -DUN_DEFINE_CONDITION_SWTICH=ON`
程序运行结果
![UN_DEFINE_CONDITION_GOOD](https://www.dennisthink.com/wp-content/uploads/2020/10/UN_DEFINE_CONDITION_GOOD.jpg)

(2) DEFINE_CONDITION未定义
CMake条件编译指令`cmake .. -DUN_DEFINE_CONDITION_SWTICH=OFF`
程序运行结果
![UN_DEFINE_CONDITION_BAD](https://www.dennisthink.com/wp-content/uploads/2020/10/UN_DEFINE_CONDITION_BAD.jpg)

4. DEBUG_LOG宏相关
(1) DEBUG_LOG定义了

CMake条件编译指令`cmake .. -DDEBUG_LOG_SWITCH=ON`

程序运行结果
![DEBUG_LOG_DEFINED](https://www.dennisthink.com/wp-content/uploads/2020/10/DEBUG_LOG_DEFINED.jpg)

(2) DEBUG_LOG未定义


CMake条件编译指令`cmake .. -DDEBUG_LOG_SWITCH=OFF`

程序运行结果
![DEBUG_LOG_NOT_DEFINED](https://www.dennisthink.com/wp-content/uploads/2020/10/DEBUG_LOG_DEFINED_NOT.jpg)

5.  DEBUG_LOG_DEFINED宏相关
(1) DEBUG_LOG_DEFINED定义了
CMake条件编译指令``
程序运行结果
![DEBUG_LOG_DEFINED](https://www.dennisthink.com/wp-content/uploads/2020/10/DEBUG_LOG_DEFINED.jpg)

(2) DEBUG_LOG_DEFINED未定义
CMake条件编译指令``
程序运行结果
![DEBUG_LOG_DEFINED_NOT](https://www.dennisthink.com/wp-content/uploads/2020/10/DEBUG_LOG_DEFINED_NOT.jpg)

6. DEFINE_FIRST和DEFINE_SECOND宏相关

(1) DEFINE_FIRST和DEFINE_SECOND宏同时定义了
CMake条件编译指令`cmake .. -DDEFINE_FIRST_SWITCH=ON -DDEFINE_SECOND_SWITCH=ON`
程序运行结果
![DEFINE_FIRST_SECOND_GOOD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINE_FIRST_SECOND_GOOD.jpg)

(1) DEFINE_FIRST和DEFINE_SECOND未同时定义
CMake条件编译指令`cmake .. -DDEFINE_FIRST_SWITCH=OFF -DDEFINE_SECOND_SWITCH=OFF`
程序运行结果
![DEFINE_FIRST_SECOND_BAD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINE_FIRST_SECOND_BAD.jpg)

7. DEFINE_THIRD和DEFINE_FOURTH宏相关

(2) DEFINE_THIRD和DEFINE_FOURTH未定义
CMake条件编译指令`cmake .. -DDEFINE_THIRD_SWITCH=OFF -DDEFINE_FOURTH_SWITCH=OFF`
程序运行结果
![NOT_DEFINE_THIRD_FOURTH_EITHER](https://www.dennisthink.com/wp-content/uploads/2020/10/NOT_DEFINE_THIRD_FOURTH_EITHER.jpg)

(2) DEFINE_THIRD定义了
CMake条件编译指令`cmake .. -DDEFINE_THIRD_SWITCH=ON`
程序运行结果
![DEFINE_THIRD_GOOD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINE_THIRD_GOOD.jpg)

(2) DEFINE_FOURTH定义了
CMake条件编译指令`cmake .. -DDEFINE_FOURTH_SWITCH=ON`
程序运行结果
![DEFINE_FOURTH_GOOD](https://www.dennisthink.com/wp-content/uploads/2020/10/DEFINE_FOURTH_GOOD.jpg)

以上就是我的所有测试结果,如果有不完善的地方,欢迎在评论区留言交流。
