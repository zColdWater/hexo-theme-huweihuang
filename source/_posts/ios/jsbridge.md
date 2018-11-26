---
title: "[WKWebView] 如何实现JSBridge基于WKWebView"
catalog: true
toc_nav_num: true
date: 2018-11-26 14:50:30
subtitle: "如何实现一个Native与Javascript的桥？基于WKWebView!"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/away.jpg"
tags:
- iOS
catagories:
- iOS
---
> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo: https://github.com/zColdWater/wkwebviewdemo

如何实现Native与Javascript的双向桥呢? 基于WKWebView
=======
> 背景概述: 随着App端H5的应用越来越多，H5同学会遇到与Native同学一样的Native需求，那么这个时候就需要 Native同学 与 H5同学能进行 通信了，那么为基于iOS的WKWebView该如何找到正确通信方式呢？  

在这里我们参考 [DSBridge-IOS](https://github.com/wendux/DSBridge-IOS)，当然如果你已经看过这个库的源码，那么此篇文章可以跳过了。我这里会根据`DSBridge`的源码介绍下它的`JSBridge`的实践

[关于WKWebView一些必要的方法使用可以点击这里](https://zcoldwater.github.io/blog/article/ios/wkwebview/)

1. 了解 Javascript ===`[调用]`===> Native
    > 如何实现 `Javascript` 调用 `Native` 呢? 其实Apple已经提供了这个通道 ,这个通道就是`WKUIDelegate`，如果还不清楚可以[点击这里查看](https://zcoldwater.github.io/blog/article/ios/wkwebview/)
    
    这里我们只提`WKUIDelegate`中的其中的一个方法，至于为什么选用这个回调方法，原因是因为 这个通道 可以让`Javascript`传递二个参数，并且还能得到一个同步的返回值。
    ```Swift
    // Javascript 调用 prompt 方法 会触发 这个代理方法 
    // 例如: var result = prompt("AA", "BB")
    func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void) {
        // 参数1 
        var param1 = prompt //"AA"
        // 参数2
        var param2 = defaultText // "BB"
        // 回调 result
        completionHandler("同步返回值 给Javascript")
    }
    ```
    

2. 了解 Native ===`[调用]`===> Javascript
    > 如何实现 `Native` 调用 `Javascript` 的方法呢，Apple同样也给我们提供了通道，
    这个通道就是`evaluateJavaScript`方法，如果你还不了解，如果还不清楚可以[点击这里查看](https://zcoldwater.github.io/blog/article/ios/wkwebview/) 
    




