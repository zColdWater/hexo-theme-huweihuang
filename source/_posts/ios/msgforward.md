---
title: "[iOS] 消息转发"
catalog: true
toc_nav_num: true
date: 2019-09-20 22:47:30
subtitle: "温习一下老生常谈的消息转发"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> Xcode11.1 Demo: https://github.com/zColdWater/MsgForwardDemo/tree/master 

# 前言
开发iOS应用的小伙伴对消息转发机制肯定是再熟悉不过了，尤其是之前`OC`语言的年代。 下面就好好整理下消息转发。

特意为文章写了[Demo](https://github.com/zColdWater/MsgForwardDemo/tree/master )，更好的去理解。

# 消息转发流程

消息转发流程的触发是: 调用一个没有实现的方法系统SDK就会触发消息转发机制。   

消息转发的流程是:   
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/msgforward1.png" height="350" />

# 补充  

`_objc_msgForward`是`IMP`类型的也就是函数实现，它是用于消息转发的，当像一个对象发送消息，但他没有实现的时候，`_objc_msgForward`会尝试做消息转发。  

其实`Aspect`就是利用替换函数实现成 `_objc_msgForward` 来触发的消息转发流程，而实现的，有兴趣的童鞋可以找找我的关于Aspect的文章。

# 总结
了解消息转发机制可以更好的帮我们做一些很不可思议的事情，比如`Aspect`等库的实现原理都用到了消息转发。  



参考:  
http://www.cocoachina.com/articles/12013  

