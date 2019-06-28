---
title: "[Nginx] Nginx常用命令"
catalog: true
toc_nav_num: true
date: 2019-06-28 19:00:30
subtitle: "使用Nginx的常用命令"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/girl-min.png"
tags:
- Nginx
catagories:
- Nginx
---

本人也是小白，这里记录一些 Nginx 常用的小知识。

前言: 介绍一些常用的Nginx命令

常用小知识:
=======

1. Nginx如何做到热部署?
2. Nginx挂了怎么办?



常用小命令:
=======
1. 查看Linux版本: `uname -a` 
2. 安装GCC编译器 `yum install -y gcc` 因为Nginx是C代码编写的
3. 安装G++编译器 `yum install -y gcc-c++` 安装C++可以编写 Nginx HTTP模块
4. 安装PCRE库 正则表达式 `yum install -y pcre pcre-devel` 如果在nigin.conf的文件内使用了正则表达式，那么需要引入这个PCRE库才可以使用正则。 pcre-devel 是二次开发所需要的库。
5. 




