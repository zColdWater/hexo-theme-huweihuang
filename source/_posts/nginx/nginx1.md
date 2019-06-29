---
title: "[Nginx] Nginx小知识"
catalog: true
toc_nav_num: true
date: 2019-06-28 19:00:30
subtitle: "这里介绍一些Nginx的小知识，可能比较零散，因为笔者也是新手，之所以比较零散也记的原因也是以此做笔记，日后再整理。"
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



使用Nginx相关小命令:
=======
1. 查看Linux版本: `uname -a` 
2. 安装GCC编译器: `yum install -y gcc` 因为Nginx是C代码编写的
3. 安装G++编译器: `yum install -y gcc-c++` 安装C++可以编写 Nginx HTTP模块
4. 安装PCRE库 正则表达式: `yum install -y pcre pcre-devel` 如果在nigin.conf的文件内使用了正则表达式，那么需要引入这个PCRE库才可以使用正则。这里要注意的是 pcre-devel 是二次开发所需要的库。
5. 安装zlib: `yum install -y zlib-devel` zlib库用于对HTTP包的内容做gzip格式的压缩，如果我们在`nginx.conf`里面配置了gzip on，并指定对于某些类型(content-type)的HTTP相应使用gzip进来进行压缩以减少网络传输量，那么，在编译时就需要把zlib编译进Nginx。
6. OpenSSL: `yum install -y openssl openssl-devel`，如果我们的服务器不止要支持HTTP，还需要在安全的SSL协议上传输HTTP，那么就需要OpenSSL了，另外，如果我们想使用MD5，SHA1等散列函数，那么也需要安装它。
7. 




