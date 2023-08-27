---
title: "gdb 7.11"
date: 2016-09-14T21:38:52+08:00
lastmod: 2016-09-14T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-cnblogs"]
comment: true
mathjax: false
---

```cpp
因为CentOS自带的GDB版本有点低，所以下载了最新的gdb 7.11编译以后，调试过程中提示以下错误。

Python Exception <type 'exceptions.NameError'> Installation error: gdb.execute_unwinders function is missing:

修改方式：

将源码目录下的python模块拷贝到指定目录即可。

Python Exception <type 'exceptions.ImportError'> No module named gdb:
gdb: warning:
Could not load the Python gdb module from `/usr/local/share/gdb/python'.
Limited Python support is available from the _gdb module.
Suggest passing --data-directory=/path/to/gdb/data-directory.

可以看出，应该放在/usr/local/share/gdb/python

gdb源码目录为

/home/dennis/DennisCode/gdb-7.11/gdb/python/lib/gdb

完全拷贝即可
```