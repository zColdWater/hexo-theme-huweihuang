---
title: "[iOS] 循环引用"
catalog: true
toc_nav_num: true
date: 2019-06-04 20:48:30
subtitle: "开发中引起循环引用的方式有哪些？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/code-min.png"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo:  

前言
=======
在我们开发iOS应用的时候，我们需要对内存实时注意，因为内存就辣么一丢丢，如果发生大量的内存泄漏，你的App很有可能被操作系统强制干掉。 所以下面的文章帮助我们知道在开发当中有哪些种情况会发生循环引用。


=======

