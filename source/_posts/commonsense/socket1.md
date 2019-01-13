---
title: "[Basic] 简单介绍Socket(套接字)"
catalog: true
toc_nav_num: true
date: 2019-01-13 15:21:30
subtitle: "什么是Socket 我们哪里用到它了？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---

注明： 本篇只是简单介绍不涉及Socket编程。

> Socket(套接字): 它是网络通信过程中端点的抽象表示，包含进行网络通信必需的五种信息：`连接使用的协议`，`本地主机的IP地址`，`本地进程的协议端口`，`远地主机的IP地址`，`远地进程的协议端口`。

> 套接字，是支持TCP/IP的网络通信的基本操作单元，可以看做是不同主机之间的进程进行双向通信的端点，简单的说就是通信的两方的一种约定，用套接字中的相关函数来完成通信过程。

非常非常简单的举例说明下:`Socket=Ip address+ TCP/UDP + port`。

白话
=======

`Socket`就是网络连接的端点。就像一根网线，`一头连到路由器`，`一头连到电脑`。  

这两端就是Socket。Socket编程就是利用一些函数开发网络应用。这些函数都与`Socket接口`有关。Socket接口可以在各种系统上使用，比如**linux，windows，mac。**

从开发的角度看，socket类似文件。这与开发文件读写程序时你打开的文件类似，只不过这是一个socket文件。

**Socket的结构Socket的结构很简单，只有三个元素，`协议`，`端口号`，`IP地址`。**

Socket程序的样子？

在C语言中，Socket函数能够返回一个Socket描述符，把它想象成文件描述符。接下来如果是客户端，使用Connect连接Socket地址，连接成功，Socket描述符就可以读写了。

服务器端.bind函数将Socket地址和Socket描述符绑定。

listen函数讲Socket描述符转化成“监听描述符”，供服务器监听客户端的请求用。

accept函数等待客户端的请求，返回“已连接描述符”，后续可以用来与客户端通信（使用底层io函数）。

以上是使用c语言进行Socket编程的几个主要函数。想了解详细信息，可以参看c语言或unix网络编程的资料。


分类
=======

**流式套接字（SOCK_STREAM）：**

    流式套接字用于提供面向连接、可靠的数据传输服务。该服务将保证数据能够实现无差错、无重复发送，并按顺序接收。流式套接字之所以能够实现可靠的数据服务，原因在于其使用了传输控制协议，即TCP（The Transmission Control Protocol）协议。

**数据报套接字（SOCK_DGRAM）：**

    数据报套接字提供了一种无连接的服务。该服务并不能保证数据传输的可靠性，数据有可能在传输过程中丢失或出现数据重复，且无法保证顺序地接收到数据。数据报套接字使用UDP（User Datagram Protocol）协议进行数据的传输。由于数据报套接字不能保证数据传输的可靠性，对于有可能出现的数据丢失情况，需要在程序中做相应的处理。

**原始套接字（SOCK_RAW）：**

    原始套接字与标准套接字（标准套接字指的是前面介绍的流式套接字和数据报套接字）的区别在于：原始套接字可以读写内核没有处理的IP数据包，而流式套接字只能读取TCP协议的数据，数据报套接字只能读取UDP协议的数据。因此，如果要访问其他协议发送数据必须使用原始套接字。


参考:  
https://www.zhihu.com/question/29637351  

