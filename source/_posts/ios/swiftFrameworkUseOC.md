---
title: "[iOS] SwiftFramework如何引用OC的对象"
catalog: true
toc_nav_num: true
date: 2018-11-07 15:46:30
subtitle: "在Swift的Framework中如何引用OC对象?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/girl-2771936.jpg"
tags:
- iOS
catagories:
- iOS
---
> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1  
> 为此做了下面的Demo，演示了如何在Swift的Framework中引用OC对象。

Demo: https://github.com/zColdWater/SwiftSwizzlingOC

具体步骤
=======

1. 创建Swift Framework  
2. 添加OC对象的 .h .m 文件
3. 在Header.h文件里面 添加OC对象的 .h 文件引用
4. Build Phases -> Headers 将之前Framework的 .h文件挪到 Public 选项卡下
5. Build -> Build Success!

图解
=======

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/SwiftSwizzlingOC1.jpg" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/SwiftSwizzlingOC2.jpg" height="250" />
