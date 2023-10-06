---
title: TinyIM架构设计
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
tags: ["TinyIM"]
comment: true
mathjax: false
---

# TinyIM架构设计

本篇文章主要介绍我设计和开发的TinyIM聊天工具的架构设计,以及这样设计的原因。

## 1. 客户端界面库的选择

目前的IM大部分都没有Linux的版本,所以我在设计这个项目的时候,希望可以把客户端做成跨平台的。在调研了Linux、Mac客户端开发技术以后,得到了以下的信息。

1. Linux平台的GUI开发技术可以使用Qt(C++)、Electron(CSS,JS,HTML)、Gtk(C++)等。
2. 在Mac平台的GUI技术可以使用Qt(C++)、Electron(CSS、JS、HTML)、Cocoa(Object-C)等。
3. 在Windows平台的GUI技术可以使用MFC(C++)、Qt(C++)、ATL(WTL、C++)、C#、Duilib、Electron(CSS、JS、HTML)等其他的界面库。
4. 个人技术栈只熟悉C++,CSS、JS和HTML需要重新学习、个人不太喜欢开发界面。

综合以上的几点,对于我目前的情况来说,最好的界面库选择是QT,其次是Electron。但是考虑到Linux用户本身不是很多,我个人也不是擅长界面的开发。
所以我在Github上找了一款已经实现的Windows的IM软件[Flamingo](https://github.com/balloonwj/flamingo),把它的界面库拿过来用,然后自己实现服务器的逻辑和网络通信的内容,这就是我对于界面库的选择。虽然这样的选择情况下,Linux和Mac的界面库没有实现。但是搭配后面的客户端结构设计,可以非常方便的用Electron来实现客户端。

## 2. 客户端的架构设计

考虑到我在选择客户端界面的时候,没有选择跨平台的Qt或者Electron。那么为了实现跨平台的客户端实现和减少对GUI技术栈的耦合,我选择了GUI和通信Core的分离,整体的结构设计如下:

![TinyIM_Client_Design](./TinyIM客户端架构设计.jpg)

**架构设计的解释:**

* 1. GUI和Core放在两个独立的进程中,以实现多个GUI复用Core的功能。
* 2. 所有数据显示的问题由GUI的代码实现,所有数据传输的问题由ClientCore的代码实现,将显示逻辑和数据传输逻辑进行了拆分。
* 3. ClientCore实现TCP接口、HTTP接口,以方便GUI采用不同技术来实现。
* 4. ClientCore和ChatServer之间实现TCP和UDP连接,以方便对不同数据通过不同的通道发送。
* 5. ClientCore之间可以实现UDP的点对点连接,降低服务器的压力。
* 6. ClientCore提供的HTTP接口,可以非常方便的实现ImRobot功能开发.

## 3. 服务器结构设计

目前的服务器没有采用微服务的模式,图片、文件、聊天消息服务器放在了一起,并且没有支持集群的部署。需要在后续的迭代中,将其逐渐的拆分开来。

## 4. Github仓库以及部分文档


TinyIM的源码仓库在[TimyIM](),文档仓库在[TimyIMDoc]()。

如果您在阅读代码和文档的过程中有什么意见和建议,希望您可以及时跟我联系。

