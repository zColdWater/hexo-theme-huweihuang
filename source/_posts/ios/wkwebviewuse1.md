---
title: "[iOS] H5在WKWebView中读取沙盒文件"
catalog: true
toc_nav_num: true
date: 2019-01-09 11:25:30
subtitle: "H5如何在WKWebView中读取沙盒文件？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/code-min.png"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo: https://github.com/zColdWater/WKWebView-LoadSandBoxResources 

需求
=======
需要加载一个在线的Web，通过`JSBridge`(不清楚的可以自行Google或者看之前我的文章)获取到Native的资源路径，比如`file:///private/xxxx/xxx/xx.png`把它显示在`WebView`当中。

举个例子吧: 
一个`HTML`的`Image`标签想显示iOS沙盒里面的资源文件。
```HTML
<image src="file:///private/xxxx/xxx/xx.png"></image>
```

看似很简单，但是经过尝试之后我才发现，并不是很容易，还是有一点坑需要踩的。


遇到问题
=======
上面看似简单的需求，但是在实现的路上却遇到了一些麻烦。 

首先我们一步一步分析:

1. 我们准备一张图片和一个视频放在沙盒当中。 
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/wkwebviewloadresources1.png" height="150" /> 
2. 拿到图片和MP4在沙盒中的路径。

3. 在HTML5当中，写一个Image和video标签，把资源路径放上去。
     <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/wkwebviewloadresources2.png" height="80" /> 

似乎看上去应该就没什么问题了。 

然后当我启动`WebServer`后把H5部署在我们本地(`模拟在线H5`)，然后Native加载这个IP就发现问题了，**啥都没显示出来**，这令我很伤心，为什么呢？  
我去Google一大圈，国内热心网友不分青红皂白说这是跨域，然后WiKi一下发现跟跨域没什么关系，如果你对跨域不了解，可以点击查看[这篇文章](https://zcoldwater.github.io/blog/article/commonsense/cors1/)。 

这里我可以这么解释为什么和跨域限制没关系，如果这算跨域，那么`Image`标签的`src`就只能同域名同端口的资源了，显然显示情况不是了，我可以加载任何域的资源。 


解决问题
=======

现在问题已经很明显了，接下来就是如何解决这个问题了，这是大家最关心的。 

1. **方法一:**
    
    如果我们从需求入手，H5同学只需要选取系统相册的一个图片，或者从系统相册中选取一个视频，那么如果可以接受系统的UI风格，完全可以用H5标签，打开相机，打开相册等，来选取手机的资源文件，在标签绑定回调方法，会接到系统给你回调的文件资源。
    
    这种解决方案，原生同学不同提供任何东西，系统已经帮你搞定了。

2. **方法二:**

    如果产品设计不能接受这个方案，我们可以使用加载离线包来实现，直白的意思就是，将H5打包成Zip，下载到手机本地，然后加载WebView的时候使用`laodFile`这个方法后面跟一个资源可访问权限的目录，你要设置一个比你访问资源目录更上一级目录作为这个根目录。 具体可以参考我的[这篇文章](https://zcoldwater.github.io/blog/article/ios/h5offline/)，当然如果加载离线包，H5同学要让服务端处理跨域的限制。

3. **方法三:**

    查了好多`Stackoverflow`的答案，普遍是将需要加载URL资源的HTML下载到本地，用`wkwebview`的`loadHTMLString`来加载这个页面，这样确实是可以使用`file协议`去加载本地资源，但是也会导致一个问题，Web资源加载拿不到，不知道是因为跨域Ajax请求还是什么情况，具体大家可以用淘宝网试试。 如果有兴趣也可以打开调试工具看下具体原因，这个解决办法要视情况而定。

4. **方法四**

    > 其实还有一种通过**Base64**的编码来解决H5Image标签显示图片的问题，当然了这里我就不介绍了，如果视频稍微大一点的家伙就不好办了，如果感兴趣可以参考[这个例子](https://github.com/DianQK/HybridDemo)，或者去Google一下就知道了。

    最后一种方法可以通过App本地开启Server服务，Root开在沙盒目录，然后通过将文件的`file协议`路径转换成本地服务路径，再给H5显示，经过测试完全可用。 

    第一步: 在`Podfile`写入(关于[GCDWebServer](https://github.com/swisspol/GCDWebServer)，一个轻量级的AppServer框架。)
    ```ruby
      pod "GCDWebServer", "~> 3.0"
    ```
    执行`pod install`安装。

    在你要使用的地方 
    ```swift
    import GCDWebServer

    /// 启动Server 参数自己定，但是directoryPath和forBasePath要定义好，我们写的是沙盒路径。
    func initWebServer() {
        let webServer = GCDWebServer()
        webServer.addGETHandler(forBasePath: "/", directoryPath: NSHomeDirectory(), indexFilename: nil, cacheAge: 3600, allowRangeRequests: true)
        webServer.start(withPort: 9999, bonjourName: "GCD Web Server")
        print("Visit \(webServer.serverURL) in your web browser")
    }
    ```
    然后启动你的App，开启Server
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/wkwebviewloadresources4.png" height="80" /> 
    然后将`file协议`换成你的App服务地址，来提供给H5如下(你可以用上面的ip，也可以localhost都一样的，都是指向你的本机。):
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/wkwebviewloadresources3.png" height="80" /> 

    这里我就是开启了一个npm的服务，这里就不再赘述了，Demo里面有大家自行运行就好了。  
    效果如下:    
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/wkwebviewloadresources5.jpeg" height="300" /> 

    不论是图片还是视频都可以加载本地资源，任务完成，Demo在文章开头。

    注意️️⚠️:  
    `**这里插一句: 如果你运行Demo要事先将图片和视频资源放在沙盒目录才行，资源文件我也放在Demo里面了，大家自己模拟器运行，打开Document文件夹自己放进去就可以了。**`


