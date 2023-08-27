---
title: wordpress博客部署.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# WordPress安装部署指南。    

之前做过一次博客的迁移，由于是部署在实体机上，所以迁移的过程比较复杂，在第一次部署在docker的过程中，花费了很多的时间。前天发现文章页面的时间信息不见了，所以又做了一次迁移，限制把这次迁移的过程记录下来，方便做博客的修复。

## 1.安装ubuntu的基础镜像。    

```console
sudo docker pull ubuntu
```


## 2.绑定端口，进入docker。    

```console
sudo docker run -p 2080:2080 -it ubuntu
```


## 3.更新相关的仓库。    

```console
apt-get update
```


## 4.安装基础的软件    

```console
apt-get install vim 
apt-get install wget 
apt-get install net-tools 
apt-get install zip
 ```


## 5.安装apache等软件    

```console
apt-get install apache2
apt-get install mysql-server
```


## 6. mysql用户名密码创建    

```console
service mysql restart
mysql
create user test identified by 'test';
```


user后的test为用户名，by之后的test为密码。

创建数据库

```console
create database wordpress;
grant all privileges on  *.* to test;
flush privileges;
exit;
```


## 7.apache2配置修改    

```console
cd /etc/apache2/
vim ports.conf
 ```


修改Listen的端口，为映射的端口本例中为2080.

```apache
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 2080

 <IfModule ssl_module>
    Listen 443
 </IfModule>

 <IfModule mod_gnutls.c>
    Listen 443
 <IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

查看IP地址

```console
ifconfig
```


![](https://www.dennisthink.com/image/2019/01/docker_ip.png)

启动apache2

```console
service apache2 restart
```


用浏览器打开,看到
172.17.0.2:2080

![](https://www.dennisthink.com/image/2019/01/apache2_succeed.png)

## 8. 安装wordpress    

```console
apt-get install php
apt-get install libapache2-mod-php
 ```


配置设置1 
![](https://www.dennisthink.com/image/2019/01/apache.png)

配置设置2
![](https://www.dennisthink.com/image/2019/01/apache_php_setting_2.png)

```console
apt-get install php-mysql
cd /var/www/html/

wget https://cn.wordpress.org/wordpress-5.0.2-zh_CN.zip

unzip wordpress-5.0.2-zh_CN.zip 
mv index.html index.html_old

mv wordpress/* .
rm wordpress -rf
rm wordpress-5.0.2-zh_CN.zip 
service apache2 restart
 ```


刷新页面看到：

![](https://www.dennisthink.com/image/2019/01/begin_install.png)
点击 ```现在就开始 ```

![](https://www.dennisthink.com/image/2019/01/setting_mysql.png)
点击 ```提交 ```

```console
vim wp-config.php
 ```


将内容写入wp-config.php，保存退出。

点击 ```现在安装 ```。

![](https://www.dennisthink.com/image/2019/01/before_login_in.png)
记录下用户名和密码，下一步登录要用。
点击安装 ```wordpress ```

进入后台

![](https://www.dennisthink.com/image/2019/01/open_back_ground.png)

## 9.安装插件主题等    

```console
/var/www/html/wp-content/plugins
 ```


### 9.1 备份插件    

```console
wget https://downloads.wordpress.org/plugin/backwpup.3.6.6.zip
```


### 9.2 markdown插件    

```console
wget https://downloads.wordpress.org/plugin/wp-editormd.zip
```


### 9.3代码高亮插件    

```console
wget https://downloads.wordpress.org/plugin/crayon-syntax-highlighter.zip
```


高亮插件设置
![](https://www.dennisthink.com/image/2019/01/high_light_setting.png)

### 9.4 静态分析插件    

```console
wget  https://downloads.wordpress.org/plugin/wp-statistics.12.5.7.zip
```


### 9.5 文章阅读量插件    

```console
wget https://downloads.wordpress.org/plugin/wp-postviews.1.76.1.zip
```


### 9.6 媒体上传大小修改插件    

```console
wget https://downloads.wordpress.org/plugin/upload-max-file-size.zip
 ```


### 9.7 下载主题    

```console
cd /var/www/html/wp-content/themes
wget https://downloads.wordpress.org/theme/nisarg.1.2.8.2.zip
```


## 10.启用插件    

启用所有插件
![](https://www.dennisthink.com/image/2019/01/enable_all_plugins.png)