---
title: "[C] Bzero"
catalog: true
toc_nav_num: true
date: 2019-02-18 14:00:30
subtitle: "Bzero使用方法"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/knowledge-min.png"
tags:
- C
catagories:
- C
---

> 将内存（字符串）前n个字节清零

头文件：#include <string.h>  

bzero() 会将内存块（字符串）的前n个字节清零，其原型为：
    void bzero(void *s, int n);

【参数】s为内存（字符串）指针，n 为需要清零的字节数。

bzero()会将参数s 所指的内存区域前n 个字节，全部设为零值。

实际上，bzero(void *s, int n) 等价于 memset((void*)s, 0,size_tn)，用来将内存块的前 n 个字节清零，但是 s 参数为指针，又很奇怪的位于 string.h 文件中，也可以用来清零字符串。


