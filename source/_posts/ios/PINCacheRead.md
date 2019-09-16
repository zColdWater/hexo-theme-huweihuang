---
title: "[iOS] 阅读PINCache源码"
catalog: true
toc_nav_num: true
date: 2019-09-16 13:10:30
subtitle: "阅读PINCache源代码，了解如何实现一个缓存框架。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---

# PINCache 

PINCache是一个iOS的三方开源库，提供了缓存的功能。  
下面我们了解一下它的实现原理。  
学习之前我们可以先了解3个东西
* `项目结构` 
* `内存缓存`
* `磁盘缓存`
  
### 项目结构

下图展示了PINCache架构

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache1.png" height="250" />

1. `PINCache`提供的方法内部都是调用的`MemoryCache`或者`DiskCache`，其实可以理解为 `PINCache类` 封装了 `MemoryCache`和`DiskCache`两个类

2. `MemoryCache` 和 `DiskCache` 也各自提供了自己的`Interface`，如果开发者愿意的话 也可以根据自己情况单独调用`MemoryCache`和`DiskCache`

### 内存缓存
内存缓存是其实就是持有一块内存不释放，然后对其添加要存储的对象，和清理要删除的对象。

**优点:** 
* 读取数据速度快`(因为内存缓存是内存对象)`

**缺点:** 
* kill app就被释放了，无法本地持久化存储。

在`PINCache`中，内存缓存使用的是`NSMutableDictionary`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache2.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache3.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache4.png" height="250" />


从上面的截图可以看出来，`PINCache`内存缓存核心用的是`NSDictionary`对象来进行存储和读取的。

### 磁盘缓存

