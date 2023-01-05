---
title: CMake区分32位64位
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

[原文链接](https://www.cnblogs.com/coolbear/p/7736392.html)

```
IF(CMAKE_CL_64)
    set(platform x64)
ELSE(CMAKE_CL_64)
    set(platform x86)
ENDIF(CMAKE_CL_64)
```