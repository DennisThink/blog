---
title: "使用telnet通过IMAP协议读取QQ邮箱"
date: 2023-05-13T21:38:52+08:00
lastmod: 2023-05-13T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["email"]
slug: use_telnet_get_email_of_qq_by_imap_protocal
comment: true
mathjax: false
---


# 使用telnet通过IMAP协议读取QQ邮箱

## 1. 连接到QQ邮箱的IMAP服务器

在命令行窗口执行: 

```shell
telnet imap.qq.com 143
```

Server:

```shell
* OK [CAPABILITY IMAP4 IMAP4rev1 ID AUTH=PLAIN AUTH=LOGIN AUTH=XOAUTH2 NAMESPACE] QQMail XMIMAP4Server ready
```

看到上面这条消息证明连接成功。


## 2. 获取服务器能力
Client:
>A01 CAPABILITY

Server:
>* CAPABILITY IMAP4 IMAP4rev1 XLIST MOVE IDLE XAPPLEPUSHSERVICE AUTH=PLAIN AUTH=LOGIN AUTH=XOAUTH2 NAMESPACE CHILDREN ID UIDPLUS                     
A01 OK CAPABILITY Completed

## 3. 无操作指令，可以用来保持或者测试连接状态
Client:
>A02 NOOP                             

Server:
>A02 OK NOOP Completed
## 4. 登录IMAP服务器
Client:
>A03 LOGIN {USERNAME} {PASSWORD}

Server:
>A03 OK Success login ok

## 5. 选择INBOX目录
Client:  
>A04 SELECT INBOX

Server:
```c
* 6 EXISTS
* 0 RECENT
* OK [UNSEEN 6]
* OK [UIDVALIDITY 1375069811] UID validity status
* OK [UIDNEXT 5258] Predicted next UID
* FLAGS (\Answered \Flagged \Deleted \Draft \Seen)
* OK [PERMANENTFLAGS (\* \Answered \Flagged \Deleted \Draft \Seen)] Permanent flags
A04 OK [READ-WRITE] SELECT complete
```
## 6. 检查INBOX目录
Client:
```c
A05 examine inbox
```
Server:
```
* 6 EXISTS
* 0 RECENT
* OK [UNSEEN 6]
* OK [UIDVALIDITY 1375069811] UID validity status
* OK [UIDNEXT 5258] Predicted next UID
* FLAGS (\Answered \Flagged \Deleted \Draft \Seen)
A05 OK [READ-ONLY] EXAMINE completent flags permitted
```
## 7. 获取INBOX目录下，所有邮件的索引
client:
```
A07 SEARCH ALL
```

Server:
```
* SEARCH 1 2 3 4 5 6 7
A07 OK SEARCH completed
```

## 8. 读取索引为7的邮件的内容
Client:
```
A08 FETCH 7 RFC822
```

Server:
```
* 7 FETCH (RFC822 {4017}
Received: from T450 ([222.130.94.131])
        by newxmesmtplogicsvrszb1-0.qq.com (NewEsmtp) with SMTP
        id 504964A2; Sun, 14 May 2023 07:20:04 +0800
X-QQ-mid: xmsmtpt1684020004tsjq8n518
Message-ID: <tencent_7ADAD39ED07339CC684E84837C5A41E85F0A@qq.com>
X-QQ-XMAILINFO: NSmesZGuqQTY17aUZ8AZFBaBNkfzqAVW2PB7scl3MGXfj2icXzR0eII5KrsNTZ
         2758kdgq0Bgx79diXqb3Ru5rEXeQypaS9aUBPug1YbzgWbFpDviJ7t5uqSn4nV0UXUNOKs4K56R
         9j90VYNvxXn2Gg19tEJIZoM/KF40Zlly2ypJQMMCJLX0RDzPjdZIFwe50Z2+i7G35fGZrsJUCNU
         oVg6f/FjwCH/AHI9gfBk+rj9+BNSwQfUNZmBk6vHfqvgJeWdcbpyKTlk1tWAJoWGPVXXzbUqg+m
         4D/rDwvM5a6cR+45mrRyanOpUHa5+Zcvtl/L+06KSjyAq79t/ecPSvprBJL3sidqLyepi7Hwaib
         hlfZCUUcomVxEJiDLOQ3klrMYDo3nbfIuMmeGl41aLWtlIAw/73ZP1D2jag7ABp4g0UE3WjiTMo
         JDJZfhxJG/yjf8WUxoYzAQ4EcjP4SQmM5aUZD4bZz2uFdphBfupEAlAM9DBuHINagKuaKGD4do3
         0IFBJcBfOx5qHsvXfBMbi7O4B/zVDDGU1xlMvvLLYPXpMHbLfQHivaE2uyE7clh+KmV7GOjw6Z8
         lDrZwfpc+OQ1QlmxUbuWgoeUzJQbEF3E6PzrdVOg5NjVMqs3/SLJaqu8WPJm8j3Dsh7XKU/JL5U
         mAoixj2V8Bb+M1eMAd6CLOPKeALXubZ4Xb8zhL7ti+/sZzLCHcDad2nA1cj/Pa2QTC+BKs7a3Sk
         FwBaltUjXuMsrC4zkuEkKQUTKQZDNHb/YkzaV8Gw2LN4IWE0aR6agPEPewCwQ+Jm+jmfjlQLX3E
         YvgqLAb8ETVnM2GKTOwSN89Z9gSi5NYiAJhs7imE2J+VvNoqJc3QU55AwHNYNr0L5PKDcLz27nK
         DClQebd7rQay7a8uZjrEYYFsw0ULF79jZNlf7Lync01HUFNBZj3XG1zNon2KGnww8tY/tFKPDyQ
         T2BPa9zuzXAgMTFuSc/Fy0SOBOj4+bxaM6k622/ipCmGXODdVKxuwhgZ87MR4ispl4NJXP4zqoz
         X0zwj7ww==
Date: Sun, 14 May 2023 07:20:04 +0800
From: "dennis_miyanhui@foxmail.com" <dennis_miyanhui@foxmail.com>
To: 2475027682 <2475027682@qq.com>
Subject: =?GB18030?B?u9i4tDogVEVTVA==?=
References: <tencent_1E6B64666C220EA4C88A5E3FC4DEE3A48F05@qq.com>
X-Priority: 3
X-GUID: F98C10F4-FBD7-44F6-B6DE-65EA1186AD44
X-Has-Attach: no
X-Mailer: Foxmail 7.2.25.179[en]
Mime-Version: 1.0
X-OQ-MSGID: <202305140720041245570@foxmail.com>
Content-Type: multipart/alternative;
        boundary="----=_001_NextPart376544028536_=----"

This is a multi-part message in MIME format.

------=_001_NextPart376544028536_=----
Content-Type: text/plain;
        charset="GB18030"
Content-Transfer-Encoding: base64

DQp0ZXN0IGVtYWlsIGZvciB0ZXN0LmRvIG5vdCByZXBseQ0KDQoNCmRlbm5pc19taXlhbmh1aUBm
b3htYWlsLmNvbQ0KIA0Kt6K8/sjLo7ogw9fR5bvUDQq3osvNyrG85KO6IDIwMjMtMDQtMTcgMTY6
MDcNCsrVvP7Iy6O6IDI0NzUwMjc2ODINCtb3zOKjuiBURVNUDQp0aGlzIGlzIHRoZSB0ZXN0DQo=

------=_001_NextPart376544028536_=----
Content-Type: text/html;
        charset="GB18030"
Content-Transfer-Encoding: quoted-printable

<html><head><meta http-equiv=3D"content-type" content=3D"text/html; charse=
t=3DGB18030"><style>body { line-height: 1.5; }blockquote { margin-top: 0px=
; margin-bottom: 0px; margin-left: 0.5em; }div.FoxDiv20230514071947815668 =
{ }body { font-size: 14px; font-family: "Segoe UI"; color: rgb(0, 0, 0); l=
ine-height: 1.5; }</style></head><body>=0A<div><span></span><br></div>=0A<=
div>test email for test.do not reply</div><hr style=3D"width: 210px; heigh=
t: 1px;" color=3D"#b5c4df" size=3D"1" align=3D"left">=0A<div><span><div st=
yle=3D"MARGIN: 10px; FONT-FAMILY: verdana; FONT-SIZE: 10pt"><div>dennis_mi=
yanhui@foxmail.com</div></div></span></div>=0A<blockquote style=3D"margin-=
Top: 0px; margin-Bottom: 0px; margin-Left: 0.5em; margin-Right: inherit"><=
div>&nbsp;</div><div style=3D"border:none;border-top:solid #B5C4DF 1.0pt;p=
adding:3.0pt 0cm 0cm 0cm"><div style=3D"PADDING-RIGHT: 8px; PADDING-LEFT: =
8px; FONT-SIZE: 12px;FONT-FAMILY:tahoma;COLOR:#000000; BACKGROUND: #efefef=
; PADDING-BOTTOM: 8px; PADDING-TOP: 8px"><div><b>=B7=A2=BC=FE=C8=CB=A3=BA<=
/b>&nbsp;<a href=3D"mailto:dennis_miyanhui@foxmail.com">=C3=D7=D1=E5=BB=D4=
</a></div><div><b>=B7=A2=CB=CD=CA=B1=BC=E4=A3=BA</b>&nbsp;2023-04-17&nbsp;=
16:07</div><div><b>=CA=D5=BC=FE=C8=CB=A3=BA</b>&nbsp;<a href=3D"mailto:247=
5027682@qq.com">2475027682</a></div><div><b>=D6=F7=CC=E2=A3=BA</b>&nbsp;TE=
ST</div></div></div><div><div class=3D"FoxDiv20230514071947815668"><div>th=
is is the test</div></div></div></blockquote>=0A</body></html>
------=_001_NextPart376544028536_=------

)
A08 OK FETCH Completed
```


## 9.列出所有目录
Client:
```
A06 LIST "" *
```

Server:
```
* LIST (\NoSelect \HasChildren) "/" "&UXZO1mWHTvZZOQ-"
* LIST (\HasNoChildren) "/" "INBOX"
* LIST (\HasNoChildren) "/" "Sent Messages"
* LIST (\HasNoChildren) "/" "Drafts"
* LIST (\HasNoChildren) "/" "Deleted Messages"
* LIST (\HasNoChildren) "/" "Junk"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/QQ&kK5O9ouilgU-"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/1143456295@qq.com"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/dennismi@163.com"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/Github"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/&kdF7Gw-"
* LIST (\HasNoChildren) "/" "&UXZO1mWHTvZZOQ-/HR"
A06 OK LIST completedn) "/" "&UXZO1mWHTvZZOQ-/&lj+RzE6R-"
```

## 10. 退出
Client:
```
A09 LOGOUT
```
Server:
```
* BYE LOGOUT received
A09 OK LOGOUT Completed
```