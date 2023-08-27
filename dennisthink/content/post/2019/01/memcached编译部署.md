---
title: memcached编译部署.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# memcached编译部署    

## 1. 下载libevent    

从 http://libevent.org/ 下载。

```console
wget https://github.com/libevent/libevent/releases/download/release-2.0.22-stable/libevent-2.0.22-stable.tar.gz
```


解压

```console
tar -xvf libevent-2.0.22-stable.tar.gz 
./autogen.sh
./configure 
make
sudo make install
 ```


## 2. 下载memcached,编译运行    

官方网址： http://memcached.org/downloads

```console
wget http://memcached.org/files/memcached-1.5.12.tar.gz
tar -xvf memcached-1.5.12.tar.gz 
./configure
make
sudo make install
```


## 3.启动memcached    

```console
memcached
```


## 4.使用Telnet连接进行测试    

注意所有的命令必须小写：

https://zhuanlan.zhihu.com/p/29919127

```console
telnet 127.0.0.1 11211

Trying 172.17.0.2...
Connected to 172.17.0.2.
Escape character is '^]'.
```


命令的格式如下:

```<command_name>  <key>  <flags>  <exptime>  <bytes>\r\n ```

### 4.1 set    

注意:set需要小写。

请求:

```console
set key1 0 0 4
key1
```


回复：

```console
STORED
```


请求:

```
set key2 0 0 8 
hell wol
 ```


回复:

```
  STORED
 ```


### 4.2 get    

请求:

```console
  get key1
 ```


回复:

```console
VALUE key1 0 4
key1
END
 ```


请求:

```console
get key2
 ```


回复:

```
VALUE key2 0 8
hell wol
END
```


### 4.3 delete    

#### 4.3.1    

请求：

```console
delete key1
```


回复:

```console
DELETED
```


#### 4.3.2    

再次请求：

```console
get key1

END
```


### 4.4 incr    

请求:

```
  set key5 0 0 1
  1
 ```


回复:

```
  STORED
 ```


incr请求:

```
  incr key5 1
 ```


回复:

```
  2
 ```


再次请求：

```
  get key5
 ```


回复:

```
  VALUE key5 0 1
  2
 ```


### 4.5 decr    

请求:

```console
decr key5 1
```


回复:

```console
2
```


请求:

```console
get key5
```


回复:

```console
VALUE key5 0 1
2
END
```
