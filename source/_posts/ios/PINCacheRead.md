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

> 前言: 这里我只介绍一下最核心的地方，至于很多细节并不提及，有兴趣的小伙伴可以查看源码

# PINCache 

PINCache是一个iOS的三方开源库，提供了缓存的功能。  
下面我们了解一下它的实现原理。  
学习之前我们可以先了解3个东西
* `项目结构` 
* `内存缓存`
* `磁盘缓存`
* `线程安全`
* `清理机制`
  
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
* 空间小，不能什么都放在内存，内存是寸土寸金的地方。

在`PINCache`中，内存缓存使用的是`NSMutableDictionary`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache2.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache3.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache4.png" height="250" />


从上面的截图可以看出来，`PINCache`内存缓存核心用的是`NSDictionary`对象来进行存储和读取的。

### 磁盘缓存
磁盘缓存是持久化存储，在iOS中一般就是存在沙盒中。

**优点:** 
* 可以持久化存储，App被Kill也无妨。
* 空间大 

**缺点:** 
* 访问大数据的速度要慢与内存缓存。

下面是`PINCache`的磁盘缓存的实现逻辑，可以看出来，PINCache磁盘缓存用了`NSKeyArchiver`和`NSKeyUnarchiver`来实现的对要存储对象的磁盘缓存。

它的存储步骤大致如下(读取相反): 
1. 用Key+固定URL拼接成唯一的FileURL
2. 在把要存的对象object通过NSKeyArchiver序列化成data
3. 最后将data存入fileURL的沙盒路径下

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache5.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache6.png" height="250" />


### 线程安全

因为涉及线程安全有很多内容，这里只介绍一下`PINCache`的线程安全是如何处理的。 

当程序中开启多线程时，并发的用`写`操作同一个变量的时候，会出现线程不安全，例如 同时2个线程同时对变量A进行`写`操作，虽然程序里的执行顺序是操作A早于操作B，但是因为是多线程并发，实际执行的时候却是操作B早于操作A，这样你的逻辑就出错了，本来你想的是用操作B的值覆盖掉操作A，现在反过来了。  

解决这个问题有很多种方法，这里只看下PINCache的实现，`PINCache`保障线程安全使用的是`条件锁`和`信号量`， 下面是它的实现代码

不懂条件锁和信号量的童鞋可以单独查一下使用和区别。

**内存缓存**: `信号量`  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache11.png" height="250" />

**磁盘缓存**: `条件锁`  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache7.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache8.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache9.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/PINCache10.png" height="250" />


### 清理机制

