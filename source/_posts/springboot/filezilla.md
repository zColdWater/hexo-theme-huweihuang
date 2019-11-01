---
title: "[Spring] 使用FileZilla连接你的服务器"
catalog: true
toc_nav_num: true
date: 2019-06-5 16:51:30
subtitle: "使用FileZilla连接你的服务器"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Spring
catagories:
- Spring
---

> 当前环境: java 12.0.1 2019-04-16 Java(TM) SE Runtime Environment (build 12.0.1+12) 备注: Mac

正文
=======

1. 进入远端服务器
2. 输入命令 `tmpftp` 会得到如下信息:
    ```
    [I 2019-06-05 10:32:17] starting temp FTP on 0.0.0.0:2121
    [I 2019-06-05 10:32:17] username is tony.zou, passwd is 554146
    ```
3. 从上面拿到 username: `tony.zou` passwd: `554146` 端口号: `2121`
4. open FileZilla 软件填写信息如下: 
   <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/filezilla.png" height="200" />

现在就可以随意和服务器进行文件交换了。

希望能帮助一些和我一样的小白。
