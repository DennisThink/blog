---
title: "Disque集群部署"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Disque集群部署

## 1. 下载代码

代码位置位于: https://github.com/antirez/disque/archive/1.0-rc1.tar.gz
可以使用wget来下载。

## 2.解压编译

```
tar -xvf 1.0-rc1.tar.gz
cd disque-1.0-rc1/
make
make test
```

## 3.启动服务开始测试

进入```src``` 目录，可以看到我们需要的程序(disque,disque-server)已经编译完成。
![](https://www.dennisthink.com/image/2019/01/disque_make_finished.png)
按照下面的目录结构创建目录
![](https://www.dennisthink.com/image/2019/01/folder_five_tree.png)

进入到对应的目录启动```disque_server``` 

因为disque启动的时候会创建nodes.conf,所以必须到对应的目录启动
单个服务启动成功的结果:
![](https://www.dennisthink.com/image/2019/01/single_disque_start_succeed.png)
全部启动成功的结果如下图所示:
![](https://www.dennisthink.com/image/2019/01/all_server_start_succeed.png)

## 4. 客户端连接## 

全部客户端连接成功的结果:![](https://www.dennisthink.com/image/2019/01/all_client_succeed.png)
查看当前的cluster.
![]("https://www.dennisthink.com/image/2019/01/start_cluster_info.png)
可以看到目前的```cluster_known_nodes``` 只有1个。
```cluster_reachable_nodes``` 有0个。
增加一个cluster以后查看cluster.
![]("https://www.dennisthink.com/image/2019/01/Add_one_cluster.png)
可以看到目前的```cluster_known_nodes``` 只有2个。
```cluster_reachable_nodes``` 有1个。
cluster全部添加好以后的结果。
![]("https://www.dennisthink.com/image/2019/01/all_cluster_finished.png)
生产者客户端生产数据。
![]("https://www.dennisthink.com/image/2019/01/add_Jobs.png)
每个消费者读取数据

* 第一个消费者
![]("https://www.dennisthink.com/image/2019/01/client1.png)


第二个消费者
![]("https://www.dennisthink.com/image/2019/01/client2.png)
第三个消费者
![]("https://www.dennisthink.com/image/2019/01/client3.png)
第四个消费者
![]("https://www.dennisthink.com/image/2019/01/client4.png)



停止一个cluster以后，查看cluster信息
![]("https://www.dennisthink.com/image/2019/01/stop_one_server.png)


可以看到可达节点数少一个。