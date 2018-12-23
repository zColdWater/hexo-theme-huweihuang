---
title: "[iOS] 如何给H5出WebView调试包"
catalog: true
toc_nav_num: true
date: 2018-12-23 23:32:30
subtitle: "App同学如何制作能给H5同学调试的ipa?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

背景
=======
随着公司开发App的多技术栈，H5的融入是必然的，Native同学与H5同学商定统一的`JSBridge`协议，来提供H5与Native的交互，那么一个问题就随之而来了，Native同学出的包要让H5同学也能开启调试`WebView`的功能，就需要在出包导出的时候注意下了。

再比如: Native和H5开发在异地，但是H5同学却想调试一下WebView中的JS代码 等等。

重点
=======
> 随便说一句，Archive选的App编译环境这个因项目而定，比如我下面使用的`Debug`下去`archive`的，当然了，每个项目 肯定都会不一样的，重要的是下面的截图。

Xcode -> Window -> Organizer -> DistributeApp

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/h5_debug.png" height="200" />

只要下面的选对了就可以啦，H5的同事就可以用你的包去调试他们的WebView里面的JS了，但是需要提前把他们的iPhone设备注册进来。





