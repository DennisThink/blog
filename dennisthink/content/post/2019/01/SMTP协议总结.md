---
title: SMTP协议总结.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# SMTP协议总结    

## 1. SMTP常用命令简介    


|命令 |含义 |示例 |
|---|---|---|
|HELO/EHLO |向服务器标识用户身份 | 
|MAIL |初始化邮件传输 |mail from:| 
|RCPT |标识单个的邮件接收人，常在MAIL 命令后面，可有多个rcpt to: | |
|DATA |在单个或多个RCPT 命令后，表示所有的邮件接收人已标识，并初始化数据传输，以. 结束。 || 
|VRFY |用于验证指定的用户/ 邮箱是否存在；由于安全方面的原因，服务器常禁止此命令 || 
|EXPN |验证给定的邮箱列表是否存在，扩充邮箱列表，也常被禁用 || 
|HELP |查询服务器支持什么命令 | 
|NOOP |无操作，服务器应响应OK | 
|QUIT |结束会话 | 
|RSET |重置会话，当前传输被取消 | 


##　2.SMTP 返回码含义


|返回码 |含义| 
|---|---|
|500 |格式错误，命令不可识别（此错误也包括命令行过长） 
|501 |参数格式错误 
|502 |命令不可实现 
|503 |错误的命令序列 
|504 |命令参数不可实现 
|211 |系统状态或系统帮助响应 
|214 |帮助信息 
|220 |服务就绪 
|221 |服务关闭传输信道 
|421 |服务未就绪，关闭传输信道（当必须关闭时，此应答可以作为对任何命令的响应） 
|250 |要求的邮件操作完成 
|251 |用户非本地，将转发向 
|450 |要求的邮件操作未完成，邮箱不可用（例如，邮箱忙） 
|550 |要求的邮件操作未完成，邮箱不可用（例如，邮箱未找到，或不可访问） 
|451 |放弃要求的操作；处理过程中出错 
|551 |用户非本地，请尝试 
|452 |系统存储不足，要求的操作未执行 
|552 |过量的存储分配，要求的操作未执行 
|553 |邮箱名不可用，要求的操作未执行（例如邮箱格式错误） 
|354 |开始邮件输入，以. 结束 
|554 |操作失败 
|535 |用户验证失败 
|235 |用户验证成功 
|334 |等待用户输入验证信息| for next connection>; 

## 3.附件功能    

要使用SMTP 发送附件, 需要对SMTP 头信息进行说明, 改变Content-type 及为每一段正文添加BOUNDARY 名。

Base64 编码函数在网络上很容易找到, 这里就不给出源码了, 如需要支持HTML 格式而又不知道如何写这些头信息, 可以用outlook 或foxmail 写一封支持HTML 格式的mail, 查看其原文信息, 依照相同的格式发送就行了.

## 4.实现抄送及密送    

在SMTP 命令集中并没有RCPT CC 或RCPT BCC 相关命令, 那要如何来实现抄送和密送功能呢?

在网络上找到这样一句话: “ 所有的接收者协商都通过RCPT TO 命令来实现，如果是BCC ，则协商发送后在对方接收时被删掉信封接收者”, 开始一直不明白这句话是什么意思? 后来通看查看foxmail 的邮件原文发现:

```email
Date: Wed, 6 Jan 2010 12:11:48 +0800
From: "carven_li"   <carven_li @smtp.com>
To: "carven"  <carven@smtp.com>
Cc: "sam"  <sam@smtp.com>,
  "yoyo"  <yoyo@smtp.com>
BCC: "clara"  <clara@tsmtp.com>
Subject: t
X-mailer: Foxmail 5.0 [cn]
Mime-Version: 1.0
Content-Type: multipart/mixed;
boundary="=====001_Dragon237244850520_====="
``` 

才恍然大悟, 所谓的” 协商” 应该就是指发送方在Data 中指定哪些为CC, 哪些为BCC, 默认情况下什么都不写, 只发送第一个RCPT TO 的mail, 其他的都被过滤掉.

## 5.SMTP身份认证    

SMTP身份认证方式有很多种, 每种认证方式验证发送的信息都有点细微的差别, 这里我主要介绍下LOGIN,PLAIN及NTLM三种简单的认证方式, 附带CRAM-MD5和DIGEST-MD5方式(验证没通过, 不知道问题出在哪了? 有待高人帮忙解决!).

要进行身份认证, 先要知道当前SMTP服务器支持哪些认证方式, 在ESMTP中有个与HELO命令相同功能的命令EHLO可以得到当前服务器支持的认证方式(有些服务器无返回信息, 可能服务器端作了限制).

### 5.1 LOGIN认证方式    

LOGIN认证方式是基于明文传输的, 因此没什么安全性可言, 如信息被截获, 那么用户名和密码也就泄露了. 认证过程如下:

```console
AUTH LOGIN
334 VXNlcm5hbWU6  //服务器返回信息, Base64编码的Username:
bXlOYW1l         //输入用户名, 也需Base64编码
334 UGFzc3dvcmQ6//服务器返回信息, Base64编码的Password::
bXlQYXNzd29yZA==//输入密码, 也需Base64编码
235 2.0.0 OK Authenticated// 535 5.7.0 authentication failed
``` 

### 5.2 NTLM认证方式    

NTLM认证方式过程与LOGIN认证方式是一模一样的, 只需将AUTH LOGN改成AUTH NTLM.就行了.

### 5.3 PLAIN认证方式    

PLAIN认证方式消息过过程与LOGIN和NTLM有所不同, 其格式为: “NULL+UserName+NULL+Password”, 其中NULL为C语言中的’\0’, 不方便使用命令行测试, 因此下面给出C++代码来实现:

```cpp
char szSend[] = "$user$pwd";
size_t n = stlen(szSend);
for(int i=0; i n; i++)
{
    if(szSend[i] == '$') 
    {
        szSend[i] = '\0';
    }
}
char szMsg[512]
base64_encode(szSend, n, szMsg);
send (skt, szMsg, strlen(szMsg), 0);
```

### 4.4 CRAM-MD5认证方式    

前面所介绍的三种方式, 都是将用户名和密码经过BASE64编码后直接发送到服务器端的, BASE64编码并不是一种安全的加密算法, 其所有信息都可能通过反编码, 没有什么安全性可言. 而CRAM-MD5方式与前三种不同, 它是基于Challenge/Response的方式, 其中Challenge是由服务器产生的, 每次连接产生的Challenge都不同, 而Response是由用户名,密码,Challenge组合而成的, 具体格式如下:

```cpp
response=base64_encode(username : H_MAC(challenge, password))
```

H_MAC是Keyed MD5算法(见http://www.faqs.org/rfcs/rfc2195.html), 先由challenge和password生成16位的散列码, 将其转换成16进制32个字节的字符串数组digest(即以%02x输出), 再对(username+空格+digest[32])进行base64编码,就是要发送的response了.
另外, 在http://www.net-track.ch/opensource/cmd5/提供了SMTP CRAM-MD5认证源码, 可用于测试CRAM-MD5认证, 但不知道是不是我这边测试的SendMail服务器配置有问题, 测试时一直不能通过.

### 5.5 DIGEST-MD5认证方式    

DIGEST-MD5认证也是Challenge/Response的方式, 与CRAM-MD5相比, 它的Challenge信息更多, 其Response计算方式也非常复杂, 我在测试时也是以认证失败而告终, 只是将在网上找到的资料整理于此, 能为后来研究的人多提供点资料, 或者有兴趣的朋友们可以和我一起讨论下.