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

```
  tar -xvf redis-5.0.3.tar.gz 
     cd redis-5.0.3/
     ls
     make
     ls
     make test
 ```


## 3. 目录设置    

 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/redis_cluster_tree.png" alt="tree" />

## 4.修改配置    

修改每个目录下的Redis的配置。
需要修改的部分如下:
1. port
 code>port 6379 ``` 改为对应的端口，比如7000，就改为  code>port 7000 ```.
2. daemonize
 code>daemonize no ```改为 code>daemonize yes ```，意思是放在后台运行
3. pidfile br />
修改 code>pidfile /var/run/redis_6379.pid ```中的6379改为该redis对应的端口 code>pidfile /var/run/redis_7000.pid ```。
4. logfile
 code>logfile "" ```改为 code>logfile "/home/basic/WorkNote/BasicNote/TestNote/Redis/Server7000/logs/redis.log" ```
5. dir
 code>dir ./ ```改为 code>dir /home/basic/WorkNote/BasicNote/TestNote/Redis/Server7000/data/ ```这里我们使用绝对路径，与每个server的路径相对应。
6. appendonly
 code>appendonly no ```改为 code>appendonly yes ```

 ol start="7">
 li>cluster-enabled
 code># cluster-enabled yes ``` 去掉 code># ``` 改为 code>cluster-enabled yes ``` /li>
 li>cluster-config-file
 code># cluster-config-file nodes-6379.conf ``` 去掉注释，改为 code>cluster-config-file nodes.conf ```。 /p> /li>
 li> p>cluster-node-timeout br />
 code># cluster-node-timeout 15000 ``` 去掉注释。 /p> /li>
 /ol>

## 5.启动服务    

```
   p>cd ./Server7000/
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
  
  所有服务启动完成。
   img src="https://www.dennisthink.com/wp-content/uploads/2019/01/all_service_start.png" alt="all_service_start_finished" />
 ```


## 6.创建CLUSTER    

 ol>
 li>redis-cli登录。
> ./redis-cli -c -p 7000 /li>
 /ol>

 *注意：一定要有 code>-c ```参数，表示cluster登录。 /*

 ol start="2">
 li>查看当前的CLUSTER的情况
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/cluster_start_1.png" alt="cluster_first" />
此时的 code>cluster_state ```为 code>fail ```。 /p> /li>
 li> p>创建CLUSTER
执行下面的命令创建CLUSTER

```
  ./redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 127.0.0.1:7007 --cluster-replicas 1
 ```
 /li>
 /ol>

创建的过程:
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/create_cluster_first_output.png" alt="cluster_create_process_1" />
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/create_cluster_second_output.png" alt="cluster_create_process_2" />

创建的结果:
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/redis_cluster_create_finished.png" alt="cluste_create_result" />

## 7.CLUSTER同步测试    

 ol>
 li>在某个节点上设置数值
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/cluster_set_data.png" alt="set_data" /> /li>
 li>在不同的节点获取数值
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/get_1_from_7004.png" alt="节点1" />
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/get_1_from_7005.png" alt="节点2" />
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/get_1_from_7007.png" alt="节点3" /> /p> /li>
 li> p>模拟节点崩溃
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/7000_master_crash.png" alt="crash" /> /p> /li>
 /ol>

 p>使用CLUSTER NODE查看信息
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/7000_master_crash_node.png" alt="crash_info" />

## 8.获取之前保存的值    

设置值
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/set_3_to_7000_node.png" alt="set_value" />
获取值
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/get_3_from_7007.png" alt="get_value" />