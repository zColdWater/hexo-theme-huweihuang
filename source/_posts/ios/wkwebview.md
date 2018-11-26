---
title: "[WKWebView] 了解WKWebView"
catalog: true
toc_nav_num: true
date: 2018-11-22 16:29:30
subtitle: "了解WKWebView相关方法。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/thegoldengatebridg.jpg"
tags:
- iOS
catagories:
- iOS
---
> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo: https://github.com/zColdWater/wkwebviewdemo


关于WKWebView
=======
> [官方说明](https://developer.apple.com/documentation/webkit/wkwebview): 从iOS 8.0和OS X 10.10开始，使用WKWebView将Web内容添加到您的应用程序。不要使用UIWebView或WebView。

> 自诩拥有 60fps 滚动刷新率、内置手势、高效的 app 和 web 信息交换通道、和 Safari 相同的 JavaScript 引擎，WKWebView 毫无疑问地成为了 WWDC 2014 上的最亮点。

1. 在应用程序的主要进程之外运行  

    > WKWebView用完了进程，这意味着它的内存与应用程序分开进行线程化; 当它超出其分配时，它将崩溃而不会崩溃应用程序（这会导致应用程序被通知并尝试重新加载页面）。 
    相比之下，UIWebView正在运行，这意味着它使用的内存被认为是应用程序占用空间的一部分，如果这超出了iOS想要分配的内容，应用程序本身将被操作系统崩溃。虽然在发生这种情况之前经常会有来自iOS的通知，这可以让我们避免崩溃，但有时这些通知不会很快返回，或者根本不会返回。  
2. 使用Nitro 一个更快的JavaScript引擎  

    > WKWebView使用也被移动Safari使用的Nitro JavaScript引擎，与UIWebView的JavaScript引擎相比，它具有显着的性能改进。  

3. 消除了某些触摸延迟  

    > UIWebView和WKWebView浏览器组件解释并将触摸事件传递给应用程序。因此，我们无法提高触摸事件的灵敏度或速度。
    触摸任何内容后， UIWebView会  延迟300ms，以确定用户是单击还是双击。这种延迟是许多用户认为基于HTML的网络应用程序“迟钝”的最主要原因之一。在WKWebView中，测试显示300ms延迟仅在快速点击（<~125 ms）后添加，iOS解释为更有可能成为双击“点击缩放”手势的一部分，而不是慢速敲击后（> ~125 ms）。


WKWebView基本方法
=======

```Swift
// 加载请求
1.webview.load()  
// 网页到上一页
2.webview.goBack()  
// 网页到下一页
3.webview.goForward()  
// 网页重新加载
4.webview.reload()  
// 网页停止加载
5.webview.stopLoading()  
// 网页标题
6.webview.title  
// 网页能够后退到上一页
7.webview.canGoBack  
// 网页能够前进到下一页
8.webview.canGoForward  
// 网页加载当中的进度
9.webview.estimatedProgress 
```

WKNavigationDelegate 协议 
=======
> [官方文档](https://developer.apple.com/documentation/webkit/wknavigationdelegate) 协议的方法可帮助您实现在Web视图接受，加载和完成导航请求的过程中触发的自定义行为。WKNavigationDelegate

```Swift
    // MARK: - WKNavigationDelegate
    
    // 【方法一】
    // 决定网页是否允许跳转。
    // WebView里面的每一次请求都会被拦截。
    // 然后通过 decisionHandler 回调参数 来决定 允许 或者 不允许。
    func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
        if let host = navigationAction.request.url?.host {
            if host == "zcoldwater.github.io" {
                // 不允许
                decisionHandler(WKNavigationActionPolicy.cancel)
                return
            }
        }
        // 允许
        decisionHandler(WKNavigationActionPolicy.allow)
    }
    
    // 【方法二】
    // 收到网页 Response 决定是否跳转
    // 1. 先经过【方法一】再经过【方法二】
    func webView(_ webView: WKWebView, decidePolicyFor navigationResponse: WKNavigationResponse, decisionHandler: @escaping (WKNavigationResponsePolicy) -> Void) {
        
        // 默认回调参数 allow 允许跳转
        decisionHandler(WKNavigationResponsePolicy.allow)
    }
    
    // 【方法三】
    // 页面内容开始加载
    func webView(_ webView: WKWebView, didStartProvisionalNavigation navigation: WKNavigation!) {
        print("页面内容正在开始加载！")
    }
    
    // 【方法四】
    // 网页内容加载失败
    func webView(_ webView: WKWebView, didFailProvisionalNavigation navigation: WKNavigation!, withError error: Error) {
        print("网页内容加载失败！")
    }
    
    // 【方法五】
    // 网页内容加载完成后，返回内容至 webview
    func webView(_ webView: WKWebView, didCommit navigation: WKNavigation!) {
        print("网页内容加载完成后，返回内容至 webview")
    }
    
    // 【方法六】
    // 网页加载完成
    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        print("网页加载完成!")
    }
    
    // 【方法七】
    // 网页返回内容至 webview 时发生失败
    func webView(_ webView: WKWebView, didFail navigation: WKNavigation!, withError error: Error) {
        print("网页返回内容至 webview 时发生失败!")
    }
    
    // 【方法八】
    // 收到网页重新定向的请求
    func webView(_ webView: WKWebView, didReceiveServerRedirectForProvisionalNavigation navigation: WKNavigation!) {
        print("收到页面重定向请求!")
    }

    // 【方法九】
    // 处理网页过程中发生终止
    func webViewWebContentProcessDidTerminate(_ webView: WKWebView) {
        print("处理网页过程中发生终止\n 内存占用过大等原因导致的系统调用此方法!")
    }
```


WKUIDelegate 协议【Javascript -> Native】
=======
> Web视图用户界面委托实现此协议以控制新窗口的打开，增强用户单击元素时显示的默认菜单项的行为，以及执行其他与用户界面相关的任务。可以在处理JavaScript或其他插件内容时调用这些方法。默认Web视图实现假定每个Web视图有一个窗口，因此非传统用户界面可能实现用户界面委托。 

```Swift

    // 创建新的webView时调用的方法
    // 内部实现根据实际需求写
    func webView(_ webView: WKWebView, createWebViewWith configuration: WKWebViewConfiguration, for navigationAction: WKNavigationAction, windowFeatures: WKWindowFeatures) -> WKWebView? {
        return nil
    }

    // 关闭webView时调用的方法
    // 内部实现根据实际需求写
    func webViewDidClose(_ webView: WKWebView) {
        print("webViewDidClose:\(webView)")
    }
    
    // 警告框
    // Javascript中调用 alert("any") 触发
    func webView(_ webView: WKWebView, runJavaScriptAlertPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping () -> Void) {
    }

    // 确认框    
    // Javascript中调用 confirm("any") 触发
    func webView(_ webView: WKWebView, runJavaScriptConfirmPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (Bool) -> Void) {
    }
    
    // 输入框
    // Javascript中调用 prompt("param1", "param2")
    func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void) {
    }

    // 能否预览用户触摸的元素
    // 根据需要实现下面协议
    func webView(_ webView: WKWebView, shouldPreviewElement elementInfo: WKPreviewElementInfo) -> Bool {
        return true
    }
```

WKWebView 必要方法【Native -> Javascript】
=======
> 如何我们要实现Native调用Javascript的时候就必须要知道下面这个方法了

```Swift
    /* @abstract Evaluates the given JavaScript string.
     @param javaScriptString The JavaScript string to evaluate.
     @param completionHandler A block to invoke when script evaluation completes or fails.
     @discussion The completionHandler is passed the result of the script evaluation or an error.
    */
    open func evaluateJavaScript(_ javaScriptString: String, completionHandler: ((Any?, Error?) -> Void)? = nil)
```


总结
=======
我们了解`WKWebView`的几个普遍的方法，和几个常用的协议，这时候我们会发现，其实Apple已经给我们提供了 Javascript 与 Native 的桥梁，通过 `evaluateJavaScript [Native -> Javascript]`, `WKUIDelegate [Javascript->Native]`，通过这两种方式 我们可以获得，Native 与 JS 互相通信的能力。


