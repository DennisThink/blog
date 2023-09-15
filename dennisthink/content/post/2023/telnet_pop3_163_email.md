---
title: "使用POP3协议连接到163邮箱"
date: 2023-09-15T21:38:52+08:00
lastmod: 2023-09-15T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["blog","history"]
slug: use-pop3-to-read-email-from-163-mail-server
comment: true
mathjax: false
---


# 使用pop3 协议获取邮件


### 1. 连接到服务器

```c
telnet 123.126.97.116 110 
```
### 2. 服务器应答
```
+OK POP3 ready
```
### 3. 发送用户名
```
USER ${YOUR_USER_NAME}
```
###  4. 服务器应答
```
+OK
```
###  5. 发送密码
```
PASS ${YOUR_PASS_WORD}
```
###  6. 服务器应答
```
+OK 50 message(s) [3505839 byte(s)]
```
###  7. 使用STAT查询状态
```
STAT
```
###  8. 服务器应答
```
+OK 50 3505839
```
###  9. 使用LIST查询邮件列表
```
LIST
```
###  10. 服务器应答
```
+OK 50 3505839
1 77493
2 56600
3 19145
4 12678
5 61640
6 12656
7 151574
8 31068
9 104881
10 99912
11 102072
12 76357
13 68811
14 67262
15 44003
16 151252
17 4487
18 97107
19 31050
20 72162
21 102324
22 102769
23 22454
24 19466
25 74950
26 95830
27 53696
28 63840
29 150977
30 96736
31 31062
32 102186
33 67516
34 49853
35 6729
36 95149
37 69535
38 52009
39 33613
40 144542
41 96570
42 31110
43 14166
44 105259
45 96493
46 31055
47 96140
48 144526
49 75028
50 38046
.

```
###  11. 使用RETR获取一封邮件
```
RETR 1
```
### 12. 服务器应答
```
+OK 4487 octets
Received: from xmbgsz5.mail.qq.com (unknown [121.14.77.108])
	by zwqz-mx-mta-g2-2 (Coremail) with SMTP id _____wDnOM8EKe1kchgzAg--.7156S3;
	Tue, 29 Aug 2023 07:08:52 +0800 (CST)
X-QQ-XMRINFO: MSVp+SPm3vtS1Vd6Y4Mggwc=
X-QQ-XMAILINFO: NKBJUaTLTIZn6oGi2/k1WANN3gNLvfs56JuB+ITUwQuBb1iioYy4enpNj7+RIA
	 4VFUO8dboBFGXsutduECGyB486FnwLOKyHl3iyaho+uIXwtHBVKKOzDEqZZQfGmjxviBtc+NvyAYW
	 9+hr+jOGnQ2KvHwbE2fuxnld90uumJlbGWzl1JKBPmn94NWwzdz2bO8PuhkN3dWimdUNcfld/WnrB
	 SW596YoobX3ekC1LP5xnOL2C7giZSBiLp3eFACEuZAn7kA9HDl/EFXISbLt32YzWSzt5BY9Y3GlwY
	 +17qjb5NMemNr+jzDbULrvRANydFE0vxdfnSs8gJvYpuyV0zx5LMuTL6732iuz+HgeQFp19EGrGRY
	 A08nj8/eF3/MjBhPKEh+oz/frNmamDRwi688WXjLlYe8kowoju8VIqAhdCbUMFkK4TL/y+cimFVeB
	 Pej4o1V2Zgng3gE5QJrApZla9Wii/033J8ZEVCEDrRWz4KU3NwBy4f5t+7QKwtkhD923AqGadn6sl
	 ml7ObV012JOtaDrPKGNWZAedk/0Xqn0Afr9dxozmoi7eJ6Ib6/Q3OeW8EarbNV0fdE15h3dDBePEt
	 3mJ2xEKXWhNpLch9WjtMN7XR3Uo0wFH6/nUfji1Rbc0PU8uf3SRfJ4UToYt1o8QvGa2F8Uyy3G+nC
	 MakU5bC56goCTpsA+opHQwf9TgLgxhg5Kq24eIQKvfyBntw5NBt4vtMg2SAa/znvgTvmXNE8DHOha
	 oyPwFBELN1P48T0ZzYwwAtX3ZmAPYG50UEO9qJ9XGRhu0u3KqWdB4gcFEEiNqp8HF5eyBUxKEA0i9
	 1p1I7971+24eb/thm3bhIxUAyulwWJC2uNH1/ApnbGtLUlTbUoOa21Dnwngo4bF9pbFn69GkEnVIr
	 ZPEYj3kEUcTFgmQeq79mS48A4cNTUYdCpR9uT/my+vLoqb0iS8u8kbp/nYdXI1DJ7FeuhO9uFsabu
	 MidkvbYq8BET/ae6ihuHbKo0fGNsU834eBCgh8lReLdtRC7+FJX/04fhw1zCr9LRCmSwvLIq9C3G6
	 PnugebpzVPoSFWdkgJG+QZCcYHq0fRbHfZRpkZksbBGjIkPw=
Received: from mailn2sh.message.cmbchina.com (mailn2sh.message.cmbchina.com [222.66.93.144])
	by newxmmxsza6-6.qq.com (NewMX) with SMTP id 1D101A16
	for <2475027682@qq.com>; Tue, 29 Aug 2023 07:07:17 +0800
X-QQ-XMRINFO: NyFYKkN4Ny6FSmKK/uo/jdU=
Date: Tue, 29 Aug 2023 07:08:51 +0800 (CST)
From: =?utf-8?B?5oub5ZWG6ZO26KGM?= <95555ad@message.cmbchina.com>
To: 2475027682@qq.com
Message-ID: <2053286275.7139059.1693264131639.JavaMail.cmbchina@mailn2sh.message.cmbchina.com>
Subject: =?utf-8?B?44CQ5rS75Yqo44CR5b6u5L+h44CB5pSv5LuY5a6d?=
 =?utf-8?B?5LuY5qy+77yM6YCJ5oup5oub5ZWG6ZO26KGM5YKo?=
 =?utf-8?B?6JOE5Y2h5pSv5LuY77yM5Y+v6aKGMC44OH4xODjlhYPnjrDph5HnuqLljIU=?=
MIME-Version: 1.0
Content-Type: multipart/mixed; 
	boundary="----=_Part_7139036_70747282.1693264131435"
X-QQ-AUTO-FWD:dennismi@163.com
X-QQ-mid:fwdxmmxsza6-6t1693264131tdsnwvmsi
X-QQ-INNER-PENDING:1
Sender:2475027682+auto_=dennismi=163.com@qq.com
X-CM-TRANSID:_____wDnOM8EKe1kchgzAg--.7156S3
Authentication-Results: zwqz-mx-mta-g2-2; spf=pass smtp.mail=247502768
	2+auto_=dennismi=163.com@qq.com;
X-Coremail-Antispam: 1Uf129KBjDUn29KB7ZKAUJUUUUU529EdanIXcx71UUUUU7v73
	VFW2AGmfu7bjvjm3AaLaJ3UbIYCTnIWIevJa73UjIFyTuYvjTRMMKtDUUUU

------=_Part_7139036_70747282.1693264131435
Content-Type: text/html; charset=utf-8
Content-Transfer-Encoding: quoted-printable

<html><head><meta name=3D"viewport" content=3D"width=3Ddevice-width,initial=
-scale=3D1,maximum-scale=3D1,minimum-scale=3D1,user-scalable=3Dno"/><style>=
img{max-width:100%;}p{margin:0px;}</style></head><body><p><a href=3D"https:=
//cmbt.cn/a/A57MAAAEU?zone_Id=3DLLUKG1PB&cust_id=3DPNCIF1293621183&bbk_org_=
id=3D129&actv_id=3D120101525&sub_actv_id=3D12000002078&tsk_id=3D12000000384=
30&tsk_ins_id=3D12000000315231&eml_track=3D5599775716"  target=3D"_blank"><=
img id=3D"img_LLUKG1OT" name=3D"77110" src=3D"http://s3gw.cmbimg.com/lu23_u=
ser11_bucket01/82f9a60d-cc7e-4cd7-8296-fcdc45022f80" data-all=3D"{&quot;id&=
quot;:&quot;LLUKG1PB&quot;,&quot;title&quot;:&quot;=E5=BF=AB=E6=8D=B7=E6=94=
=AF=E4=BB=98=E9=A2=86=E7=BA=A2=E5=8C=85&quot;,&quot;url&quot;:&quot;https:/=
/actship-activityui.paas.cmbchina.com/ActPage.html?activityId=3DAGP20230818=
1541183OH8Gh4T&quot;,&quot;way&quot;:&quot;2&quot;}" style=3D"max-width:100=
%;"/></a></p></body></html> =E5=A6=82=E4=B8=8D=E6=83=B3=E6=94=B6=E5=88=B0=
=E6=AD=A4=E7=B1=BB=E9=82=AE=E4=BB=B6,<a  target=3D"_blank" href=3D"http://s=
as.cmbchina.com/emlTd?data=3D43QE3CC389A70C808EC81A0CF8DB924A8306FED1EDB223=
8AADBB027ABEA543E05280E2C153F98B642E6524EA26B80492315BFD5596FD896E2B019692A=
A0AE5821F88A378B28E35C53531367643DFE0AB07E27782F47B77FCE24AC4D9F8299E677F89=
FE87AB6073AD82A189C42072C7D08CB87549C37821D75E6795B533CEEEFDF0304IGG">=E7=
=82=B9=E5=87=BB=E9=80=80=E8=AE=A2</a><img src=3D"http://infclc.paas.cmbchin=
a.com/emailOpnCustFdbk?cust_id=3DPNCIF1293621183&bbk_org_id=3D129&actv_id=
=3D120101525&sub_actv_id=3D12000002078&tsk_id=3D1200000038430&tsk_ins_id=3D=
12000000315231&type=3D2" width=3D"0" height=3D"0"/>
------=_Part_7139036_70747282.1693264131435--
.



```