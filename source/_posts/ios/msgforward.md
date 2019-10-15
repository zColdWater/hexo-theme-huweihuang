---
title: "[iOS] 消息转发"
catalog: true
toc_nav_num: true
date: 2019-09-20 22:47:30
subtitle: "温习一下老生常谈的消息转发"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> Xcode11.1 Demo: https://github.com/zColdWater/MsgForwardDemo/tree/master 

# 前言
开发iOS应用的小伙伴对消息转发机制肯定是再熟悉不过了，尤其是之前`OC`语言的年代。 下面就好好整理下消息转发。


# 消息转发流程

消息转发流程的触发是: 调用一个没有实现的方法系统SDK就会触发消息转发机制。   

消息转发的流程是:   
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/msgforward1.png" height="350" />






