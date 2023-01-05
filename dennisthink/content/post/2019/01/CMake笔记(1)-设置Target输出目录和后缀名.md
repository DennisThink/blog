---
title: "CMake笔记(1)-设置Target输出目录和后缀名"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

原文链接:[原文链接]("https://www.cnblogs.com/tangxin-blog/p/8283460.html")

## 一.

```
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../bin) 
```

上面的语句能设置可执行文件的输出目录

在Win + VS环境下,会自动在你所设置的目录后面扩展一层  CONFIG> 目录,所以最终生成的Debug版本程序会在```${PROJECT_SOURCE_DIR}/../bin/Debug``` 目录下,Release版本程序会在 ```${PROJECT_SOURCE_DIR}/../bin/Release``` 目录下.

在Linux + GCC环境下,无论是Debug还是Release,生成的可执行程序会直接放在你所设置的目录下,不会有差异.

## 二.

```
SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../lib) 
```

上面的语句能设置库文件的输出目录

在Win + VS环境下,会自动在你所设置的目录后面扩展一层  CONFIG> 目录,所以最终生成的Debug版本库会在 ```${PROJECT_SOURCE_DIR}/../lib/Debug``` 目录下,Release版本库会在 ```${PROJECT_SOURCE_DIR}/../lib/Release``` 目录下.

在Linux + GCC环境下,无论是Debug还是Release,生成的库文件会直接放在你所设置的目录下,不会有差异.

### 三.

```
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../bin)     
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../bin) 
```

上面两条语句分别设置了Debug版本和Release版本可执行文件的输出目录,

一旦设置上面的属性,在任何环境下生成的可执行文件都将直接放在你所设置的目录.

##　四.
```set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../lib)    
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../lib) 
```

上面两条语句分别设置了Debug版本和Release版本库文件的输出目录,

一旦设置上面的属性,在任何环境下生成的库文件都将直接放在你所设置的目录.

## 五.

```
set(CMAKE_DEBUG_POSTFIX "_d")    
set(CMAKE_RELEASE_POSTFIX "_r") 
```

上面两条语句分别设置了Debug版本和Release版本下库文件的后缀名.

## 六.
```
set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d")     
set_target_properties(${TARGET_NAME} PROPERTIES RELEASE_POSTFIX "_r") 
```

上面两条语句分别设置了Debug版本和Release版本下可执行文件的后缀名.