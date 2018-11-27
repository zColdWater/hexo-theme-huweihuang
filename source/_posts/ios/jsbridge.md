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

前提掌握
=======
> 背景概述: 随着App端H5的应用越来越多，H5同学会遇到与Native同学一样的Native需求，那么这个时候就需要 Native同学 与 H5同学能进行 通信了，那么为基于iOS的WKWebView该如何找到正确通信方式呢？  

在这里我们参考 [DSBridge-IOS](https://github.com/wendux/DSBridge-IOS)，当然如果你已经看过这个库的源码，那么此篇文章可以跳过了。我这里会根据`DSBridge`的源码介绍下它的`JSBridge`的实践

[关于WKWebView一些必要的方法使用可以点击这里](https://zcoldwater.github.io/blog/article/ios/wkwebview/)

1. **了解 Javascript ===`[调用]`===> Native**
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
    

2. **了解 Native ===`[调用]`===> Javascript**
    > 如何实现 `Native` 调用 `Javascript` 的方法呢，Apple同样也给我们提供了通道，
    这个通道就是`evaluateJavaScript`方法，如果你还不了解，如果还不清楚可以[点击这里查看](https://zcoldwater.github.io/blog/article/ios/wkwebview/) 

    我们事先准备好 JS方法 `nativeMessageHandler` 然后在代码中 找到一个时机，
    调用即可 触发`Javascript方法`。

    ```Javascript
    function nativeMessageHandler(method,param) {
        // 'action1','param1'
        console.log(method,param) 
    }
    ```

    ```Swift
    // Native 代码

    // 注意: 此方法 只有在整个WebView都加载完成 才会有反应。
    webview.evaluateJavaScript("nativeMessageHandler('action1', 'param1')", completionHandler: { (feedback, error) in
    })
    ```

了解DSBridge-IOS原理
=======
> 下面是纯干货了，至于为什么我们研究`DSBridge-IOS`这个库，是因为它能够满足我们开发的大部分场景，是基于`WKWebView`实现`JSBridge`的比较好的实现，那么我们就看看它的工作原理是什么，这样也有助于我们使用这种库。   

我们只会针对其中的两种方法进行研究其原理 ，如果你没使用过可以去看下再回来可能会更好。[DSBridge-IOS](https://github.com/wendux/DSBridge-IOS)

下面是`DSBridge`与`Native`交互的两种方式: 一种是`JS`调用`Native`方法，另一种是`Native`调用`JS`方法  

**1. 同步调用 `使用DSBridge第三方`如下:**
    ```Javascript
    // 同步 调用
    function callSyn() {

        // 对应原生方法名: `testSyn`
        // 参数: `Hello` 
        // 类型: 同步调用
        dsBridge.call("testSyn", "Hello")
    }
    ```
    **实现原理**: 在`DSBridge`里面是通过[`WKUIDelegate`](https://zcoldwater.github.io/blog/article/ios/wkwebview/)去实现同步调用的，JS会通过调用`prompt`方法，唤起`Native`层的`WKUIDelegate`代理协议中的下面的方法 
    ```Swift
    func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void) {
    }
    ```
    然后这个协议回调方法里面，通过`JS`给`Native`的类名，方法名，通过OC的Runtime去动态的调用此方法，得到返回值，并且返回给回调函数`completionHandler` 实现`Native`返回值给`JS`方法。

**2. 异步调用 `使用DSBridge第三方`如下:**
    ```Javascript
    // 异步 调用
    function callAsyn() {

        // 对应原生方法名: `testAsyn`
        // 参数: `hello`
        // 类型: 异步调用
        dsBridge.call("testAsyn","hello", function (value) {
            // 得到原生回调 参数。
            console.log(value)
        })
    }
    ```
    **实现原理**: 在`DSBridge`里面是通过[`WKUIDelegate`](https://zcoldwater.github.io/blog/article/ios/wkwebview/)和`evaluateJavaScript`方法来实现的`JS`调用`Native`然后`Native`回调给`JS`的。 
    首先，`JS`方法调用`Native`附加回调参数，`JS`会把这个回调函数注册在`JS`全局下，比如说，挂在全局叫`BlockA`，然后继续通过调用`prompt`方法触发`WKUIDelegate`协议回调，方法在下面。
    ```Swift
    func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void) {
    }
    ```
    然后在拿到`WKUIDelegate`的回调后，`Native`还会通过传来类名，方法名，根据OC Runtime去动态调用方法，然后拿到同步返回值，给`completionHandler`方法调用，返回给JS，当通过Runtime动态调用到`Native`方法内的时候，我们触发回调参数，来回调给`JS`，这步就需要方法`evaluateJavaScript`来实现，因为当初我们挂在整个`JS`下有一个回调函数，并且起了名，现在就需要回调给写调用`JS`这个全局的回调，并且把结果告知给它。

下面是整体流程图:  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/JSBridge.png" height="350" />


总结
=======
上面我们分析了实现方式，无非都是用系统提供的与JS的通信能力，再来借助一下动态运行时来完成，虽然外表看起来神秘，但是经过源码阅读后，会发现其实还是很简单的。 以上全部是原作，如有建议和提议，不妨在下面留言，希望能对一些朋友有所帮助。