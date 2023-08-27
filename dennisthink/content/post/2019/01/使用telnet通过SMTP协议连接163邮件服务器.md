---
title: 使用telnet通过SMTP协议连接163邮件服务器.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

telnet 连接163服务器发送邮件的命令:
使用下面的命令连接到163的服务器。

```console
telnet smtp.163.com 25
```


下面是控制台的交互数据
其中
```S: ```之后是接收到来自服务器的数据
```C: ```之后，是客户端发送的数据。

连接到服务器:

```console
Trying 220.181.12.12...
Connected to smtp.163.com.
Escape character is '^]'.

S:220 163.com Anti-spam GT for Coremail System (163com[20141201])
C:EHLO smtp.163.com
S:250-mail
S:250-PIPELINING
S:250-AUTH LOGIN PLAIN
S:250-AUTH=LOGIN PLAIN
S:250-coremail 1Uxr2xKj7kG0xkI17xGrU7I0s8FY2U3Uj8Cz28x1UUUUU7Ic2I0Y2UrmYPAAUCa0xDrUUUUj
S:250-STARTTLS
S:250 8BITMIME
```

登录服务器:

```console
C:AUTH LOGIN
S:334 dXNlcm5hbWU6
C:Base64(userName)
S:334 UGFzc3dvcmQ6
C:Base64(password)
S:235 Authentication successful
```

注意：
用户名为邮箱，例如test@163.com
密码为客户端验证码.

发送邮件：

```console
C:MAIL FROM:  <dennismi@163.com>
S:250 Mail OK
C:RCPT TO:  <dennismi1024@gmail.com>
S:250 Mail OK
C:DATA
S:354 End data with  <CR> <LF>. <CR> <LF>
C:Subject:Design Plan
C:From:"" dennismi@163.com>
C:To:"" dennismi1024@gmail.com>


.

Connection closed by foreign host.
```