---
title: Redis 集群配置学习.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Redis 集群配置学习    

## 1. 下载代码    

从 https://github.com/antirez/redis/releases 下载最新的Release版本代码，目前我使用的是5.0.3的版本。
使用wget下载代码。

## 2. 代码编译    

```console
tar -xvf redis-5.0.3.tar.gz 
cd redis-5.0.3/
ls
make
ls
make test
 ```


## 3. 目录设置    

 ![](https://www.dennisthink.com/image/2019/01/redis_cluster_tree.png)tree" />

## 4.修改配置    

修改每个目录下的Redis的配置。
需要修改的部分如下:
1. port
 ```port 6379 ``` 改为对应的端口，比如7000，就改为  ```port 7000 ```.
2. daemonize
 ```daemonize no ```改为 ```daemonize yes ```，意思是放在后台运行
3. pidfile
修改 ```pidfile /var/run/redis_6379.pid ```中的6379改为该redis对应的端口 ```pidfile /var/run/redis_7000.pid ```。
4. logfile
 ```logfile "" ```改为 ```logfile "/home/basic/WorkNote/BasicNote/TestNote/Redis/Server7000/logs/redis.log" ```
5. dir
 ```dir ./ ```改为 ```dir /home/basic/WorkNote/BasicNote/TestNote/Redis/Server7000/data/ ```这里我们使用绝对路径，与每个server的路径相对应。
6. appendonly
 ```appendonly no ```改为 ```appendonly yes ```

 ```# cluster-enabled yes ``` 去掉 ```# ``` 改为 ```cluster-enabled yes ``` /li>
 
 luster-config-file
 
 ```# cluster-config-file nodes-6379.conf ``` 去掉注释，改为 ```cluster-config-file nodes.conf ```。 
 cluster-node-timeout 
 ```# cluster-node-timeout 15000 ``` 去掉注释。 


## 5.启动服务    

```console
cd ./Server7000/
ls
./redis-server ./conf/redis.conf 
cd ../Server7001
./redis-server ./conf/redis.conf 
cd ../Server700
./redis-server ./conf/redis.conf 
cd ../Server7003
./redis-server ./conf/redis.conf 
cd ../Server7004
./redis-server ./conf/redis.conf 
cd ../Server7005
./redis-server ./conf/redis.conf 
cd ../Server7006
./redis-server ./conf/redis.conf 
cd ../Server7007
./redis-server ./conf/redis.conf
```

所有服务启动完成。
![](https://www.dennisthink.com/image/2019/01/all_service_start.png)



## 6.创建CLUSTER    

redis-cli登录。

```console
./redis-cli -c -p 7000 
```

 *注意：一定要有 ```-c ```参数，表示cluster登录。

* 查看当前的CLUSTER的情况
 ![](https://www.dennisthink.com/image/2019/01/cluster_start_1.png)
此时的 ```cluster_state ```为 ```fail ```。 
* 创建CLUSTER
执行下面的命令创建CLUSTER

```console
./redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 127.0.0.1:7007 --cluster-replicas 1
 ```


创建的过程:
![](https://www.dennisthink.com/image/2019/01/create_cluster_first_output.png)
![](https://www.dennisthink.com/image/2019/01/create_cluster_second_output.png)

创建的结果:
 ![](https://www.dennisthink.com/image/2019/01/redis_cluster_create_finished.png)

## 7.CLUSTER同步测试    


* 在某个节点上设置数值
 ![](https://www.dennisthink.com/image/2019/01/cluster_set_data.png)
* 在不同的节点获取数值
 ![](https://www.dennisthink.com/image/2019/01/get_1_from_7004.png)
 ![](https://www.dennisthink.com/image/2019/01/get_1_from_7005.png)
 ![](https://www.dennisthink.com/image/2019/01/get_1_from_7007.png) 

* 模拟节点崩溃
![](https://www.dennisthink.com/image/2019/01/7000_master_crash.png) 
 

 * 使用CLUSTER NODE查看信息
 ![](https://www.dennisthink.com/image/2019/01/7000_master_crash_node.png)

## 8.获取之前保存的值    

设置值                   
![](https://www.dennisthink.com/image/2019/01/set_3_to_7000_node.png)

获取值                      
 ![](https://www.dennisthink.com/image/2019/01/get_3_from_7007.png)