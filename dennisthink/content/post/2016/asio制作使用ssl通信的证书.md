---
title: "asio制作使用ssl通信的证书"
date: 2016-12-14T21:38:52+08:00
lastmod: 2016-12-14T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-cnblogs"]
comment: true
mathjax: false
---

1,生成ca的key
```console
openssl genrsa -out ca.key 1024/2048 (with out password protected)
openssl genrsa -des3 -out ca.key 1024/2048 (password protected)
```
2,根据ca的key,生成顶级证书
```console
openssl req -new -x509 -key ca.key -out ca.pem -days 1095
```
输入:
```console
Country Name (2 letter code) [AU]:CA
State or Province Name (full name) [Some-State]:CA
Locality Name (eg, city) []:CA
Organization Name (eg, company) [Internet Widgits Pty Ltd]:CA
Organizational Unit Name (eg, section) []:CA
Common Name (e.g. server FQDN or YOUR name) []:CA
Email Address []:CA
```


2,客户端证书
(1)生成客户端的key
```console
openssl genrsa -out server.key 2048
```
(2)生成客户端证书请求
```console
openssl req -new -key app.key -out app.csr
```
创建两个目录(需要根据你的openssl.cnf的提示)
```console
mkdir ./demoCA/
mkdir ./demoCA/newcerts

cd ./demoCA/
touch index.txt
echo "01">>serial
cd ..
```

(3)生成证书
```console
openssl ca -in server.csr -out server.pem -cert ca.pem -keyfile ca.key -days 1826 -policy policy_anything
```
输入的信息:
```console
Sign the certificate? [y/n]:y
1 out of 1 certificate requests certified, commit? [y/n]y
```
(4)证书的位置
```/demoCA/newcerts/01.pem```

3,为了在TLS中使用
 
需要进行以下几步

(1). 用vim将01.pem打开,删除 -----BEGIN CERTIFICATE----- 之前的内容,保存                 
(2). 将server.key的内容放到01.pem之后
cat server.key >>01.pem            
(3). 将ca.pem的内容放到01.pem之后
cat ca.pem >>01.pem            
(4)讲ca.key的内容放到ca.pem之后
cat ca.key >>ca.pem          

(4)证书制作完毕,制作dh证书
```console
openssl dhparam -out dh1024.pem 1024
```
4,所有过程进行完毕

