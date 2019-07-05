---
title: "[iOS] 检测OC项目中未使用的方法"
catalog: true
toc_nav_num: true
date: 2019-07-05 23:40:30
subtitle: "如何检查OC项目中未使用的方法呢？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/piano1.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1 

# 检查项目中ObjectiveC 未使用 方法

# 准备工作
可执行文件 我们以NioApp为例:   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/p_access_file1.png" width = "800" height="400" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/p_access_file2.png" width = "300" height="300" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/p_access_file3.png" width = "400" height="300" />


# 工具地址
检测工具: https://github.com/nst/objc_cover


## 使用
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/p_access_file4.png" width = "800" height="400" />

## 输出
```Objective-C
-[NIOServeFunctionCell setFunctionImageW:]
-[NIOServeFunctionCell setFunctionLabel:]
-[NIOServeFunctionCell setFunctionTab:]
-[NIOServeFunctionCell setImageBottom:]
+[NIOServeShopListModel mj_objectClassInArray]
-[NIOServeShopListModel .cxx_destruct]
-[NIOServeShopListModel findPower]
-[NIOServeShopListModel nioHouses]
-[NIOServeShopListModel setFindPower:]
-[NIOServeShopListModel setNioHouses:]
-[NIOServeShopListModel setTabs:]
-[NIOServeShopListModel tabs]
-[NIOServiceBlock .cxx_destruct]
-[NIOServiceItem .cxx_destruct]
-[NIOServiceItemDefault .cxx_destruct]
-[NIOServiceItemDefault setItemImageView:]
-[NIOServiceItemDefault setItemSubTitle:]
-[NIOServiceItemDefault setItemTitle:]
-[NIOServiceItemDefaultCell .cxx_destruct]
-[NIOServiceItemDefaultCell setServiceItemCellWithData:]
-[NIOServiceItemDetail .cxx_destruct]
-[NIOServiceItemDetail setItemImageView:]
-[NIOServiceItemDetail setItemTitle:]
-[NIOServiceSectionModel .cxx_destruct]
-[NIOSetUserFtueAPI .cxx_destruct]
-[NIOSetUserFtueAPI requestMethod]
-[NIOSetUserFtueAPI requestTimeoutInterval]
-[NIOSetUserFtueAPI shouldFullSignForURM]
-[NIOSetUserFtueAPI shouldSign]
-[NIOShareActivity .cxx_destruct]
-[NIOShareInfo .cxx_destruct]
-[NIOShareManager .cxx_destruct]
-[NIOShareManager getUMSocialPlatformTypeByChannel:]
-[NIOShareManager shareToPlatform:args:resultBlock:]
-[NIOShareManager showActivityViewWithActionFuncs:actionResultHandler:]
-[NIOShareManager showActivityViewWithShareModel:shareResultHandler:]
-[NIOSheet .cxx_destruct]
-[NIOSheet setCancelShowSheet:]
```


## 总结
此脚本方法只能检测OC可能未使用的方法，不适用其他场景。python依赖otool作为工具，原理利用 Mach-O 文件的结构和展示内容。  

`__TEXT:__objc_methname:`中包含了代码中的所有方法，而`__DATA__objc_selrefs`中则包含了所有被使用的方法的引用，通过取两个集合的差集就可以得到所有未被使用的代码。

