---
title: "[Basic] 理解SSL/TLS协议"
catalog: true
toc_nav_num: true
date: 2019-01-10 11:06:30
subtitle: "什么是SSL/TLS协议？"
header-img: "https://upload-images.jianshu.io/upload_images/1793544-35a59cc66546fe40.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"
tags:
- Basic
catagories:
- Basic
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

什么是SSL/TLS协议
=======

> 本文简要介绍SSL/TLS协议的运行机制。文章的重点是设计思想和运行过程，不涉及具体的实现细节。如果想了解这方面的内容，请参阅[RFC文档](http://tools.ietf.org/html/rfc5246)。

**先概述下历史，为什么是SSL/TLS协议:**  
```
1994年，NetScape公司设计了SSL协议（Secure Sockets Layer）的1.0版，但是未发布。
1995年，NetScape公司发布SSL 2.0版，很快发现有严重漏洞。
1996年，SSL 3.0版问世，得到大规模应用。
1999年，互联网标准化组织ISOC接替NetScape公司，发布了SSL的升级版TLS 1.0版。
2006年和2008年，TLS进行了两次升级，分别为TLS 1.1版和TLS 1.2版。最新的变动是2011年TLS 1.2的修订版。
``` 
目前，应用最广泛的是`TLS 1.0`，接下来是`SSL 3.0`。但是，主流浏览器都已经实现了`TLS 1.2`的支持。
`TLS 1.0`通常被标示为`SSL 3.1`，`TLS 1.1`为`SSL 3.2`，`TLS 1.2`为`SSL 3.3`。





