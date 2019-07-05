---
title: "[iOS] 检测Swift项目中未使用的类，方法，属性"
catalog: true
toc_nav_num: true
date: 2019-07-05 23:34:30
subtitle: "如何检查Swift项目中未使用的类，方法，属性呢？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/piano1.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1 

# 检查项目中Swift 类，方法，属性 未使用情况

# 项目地址
`homepage:` https://github.com/zColdWater/swift-scripts

## 使用
```
1. cd <项目根目录>
2. 将unused.rb脚本文件 路径填入终端即可， 或者直接将 unused.rb 拖拽至终端。
```

## 输出
```
Total items to be checked 13
Total unique items to be checked 13
Starting searching globally it can take a while
Item< var oop [] from: TestDemo/ViewController.swift:13:0>
Item< var poo [] from: TestDemo/ViewController.swift:15:0>
Item< func bcd [] from: TestDemo/ViewController.swift:29:0>
Item< class AppDelegate [] from: TestDemo/AppDelegate.swift:12:0>
Item< var window [] from: TestDemo/AppDelegate.swift:14:0>
Item< func applicationWillResignActive [] from: TestDemo/AppDelegate.swift:22:0>
Item< func applicationDidEnterBackground [] from: TestDemo/AppDelegate.swift:27:0>
Item< func applicationWillEnterForeground [] from: TestDemo/AppDelegate.swift:32:0>
Item< func applicationDidBecomeActive [] from: TestDemo/AppDelegate.swift:36:0>
Item< func applicationWillTerminate [] from: TestDemo/AppDelegate.swift:40:0>
```

## Xcode 集成
添加自定义Build脚本 *Custom Build Phase/Run Script*  
`~/Projects/swift-scripts/unused.rb xcode`  
![](https://user-images.githubusercontent.com/119268/32348473-88080ed2-c01c-11e7-9de6-762aeb195156.png)
![](https://user-images.githubusercontent.com/119268/32348476-8af3a700-c01c-11e7-893f-013851568882.png)

## 已知问题:
- 像系统代理方法，或者给其他模块的 API，都会被标记为 unuse ，所以要人工确认，才可以去掉多余方法。






