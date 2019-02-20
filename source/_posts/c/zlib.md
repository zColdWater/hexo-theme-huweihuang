---
title: "[C] ZLIB"
catalog: true
toc_nav_num: true
date: 2019-02-20 14:47:30
subtitle: "如何简单使用ZLIB"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/knowledge-min.png"
tags:
- C
catagories:
- C
---

> zlib手册地址: https://www.zlib.net/manual.html


前言
=======

**我们先了解下 tar gz zip 7z 有什么区别?**

1) tar是Linux常见的一种归档文件（原生不包括压缩功能）gzip是gnu/Linux的一种压缩文件工具，算法是基于 DEFLATE，文件是gz，可以和tar组合。

2) zip是一种规范开放的压缩文件，算法不定，但主要是用 DEFLATErar的算法专有，但发行时附送解码器允许解码器再开发，编码器专有7zip和zip差不多，算法不定，主要用bzip2和lzma，而且完全开源。

3) zip和7z更像是压缩容器，因为算法不是格式固定，允许支持其他压缩算法


**zip vs gzip:**

1) zip和gzip(gz)不兼容，虽然它们都是使用相同的deflate压缩算法
2) zip更像一个打包器，能把多个多件放到一个zip中；gzip一次只对一个文件压缩，通常与tar命令一起用

使用zlib压缩
=======

> 使用 zlib 库实现压缩



