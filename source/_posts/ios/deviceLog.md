---
title: "[iOS] 输出日志信息到系统控制台"
catalog: true
toc_nav_num: true
date: 2019-07-22 15:50:30
subtitle: "iOS如何在App里面打印的信息输出到系统控制台里面？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

前言
=======

> 这里仅仅只是记录一下这个过程，帮助也有同样需求的同学，也方便以后自己查看。

最近接到测试同学反馈的App的Bug，在排查问题的时候，就需要查看手机日志。  

**这里说下，只有`Objective-C`里面的`NSLog`函数才可以在系统的控制台里面输出，`Swift`里面的`Print`函数是无法在系统的控制台输出出来的。**


过程
=======

一， 项目中使用`NSLog`输出问题日志，或者其他信息。

二， 打开Device。
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/systemlog_control1.png" height="350" />

三， 打开控制台。
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/systemlog_control.png" height="350" />




