---
title: "[Basic] 简单介绍Socket"
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

Unix域套接字
=======

> Wiki中的介绍

`Unix domain socket`或者`IPC socket`是一种终端，**可以使同一台操作系统上的两个或多个进程进行数据通信。**与管道相比，`Unix domain sockets`既可以使用字节流，又可以使用数据队列，而管道通信则只能使用字节流。`Unix domain sockets`的接口和`Internet socket`很像，但它不使用网络底层协议来通信。`Unix domain socket`的功能是`POSIX`操作系统里的一种组件。  

Unix domain sockets 使用系统文件的地址来作为自己的身份。它可以被系统进程引用。所以两个进程可以同时打开一个Unix domain sockets来进行通信。不过这种通信方式是发生在系统内核里而不会在网络里传播。


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


参考:  
https://www.zhihu.com/question/29637351  

