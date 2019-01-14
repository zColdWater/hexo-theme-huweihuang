---
title: "[Basic] 理解TCP"
catalog: true
toc_nav_num: true
date: 2019-01-13 16:37:30
subtitle: "什么是TCP?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---


什么是TCP
=======

> 下面解释来自WiKi

传输控制协议（英语：Transmission Control Protocol，縮寫為TCP）是一种面向连接的、可靠的、基于字节流的传输层通信协议，由IETF的RFC 793定义。 在简化的计算机网络OSI模型中，它完成第四层传输层所指定的功能，用户数据报协议（UDP）是同一层内另一个重要的传输协议。

白话，TCP传输协议是可靠的传输协议。


TCP 三次握手
=======

> TCP用三路握手（或称三次握手，three-way handshake）过程创建一个连接。在连接创建过程中，很多参数要被初始化，例如序号被初始化以保证按序传输和连接的强壮性。

**建立过程:**

1. 客户端通过向服务器端发送一个`SYN`来创建一个主动打开，作为三路握手的一部分。客户端把这段连接的序号设定为随机数A。

2. 服务器端应当为一个合法的`SYN`回送一个`SYN/ACK`。`ACK`的确认码应为`A+1`，`SYN/ACK`包本身又有一个随机产生的序号`B`。

3. 最后，客户端再发送一个`ACK`。当服务端收到这个`ACK`的时候，就完成了三路握手，并进入了连接创建状态。此时包的序号被设定为收到的确认号`A+1`，而响应号则为`B+1`。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/TCP1.png" height="300" />


TCP 传输信息
=======

> 下图描述 建立好通信连接后的示意图。

展示TCP传输与UDP传输的不同，也展示了为什么TCP更加稳定。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/TCP2.png" height="300" />



TCP 关闭连接
=======

在日常的生活中，浏览器访问完网站后，关闭网站，此时断开连接，服务端肯定也会要断开连接，防止过度占用资源。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/TCP3.png" height="300" />


参考:  
https://www.youtube.com/watch?v=TEh6t8meORo