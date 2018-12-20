---
title: "[iOS] 实现H5离线包机制"
catalog: true
toc_nav_num: true
date: 2018-12-20 14:50:30
subtitle: "如何实现H5离线包机制，以及里面使用到了哪些东西？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/nature.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

H5本地包下载: https://github.com/zColdWater/OfflinePack

前言
=======
> 距离我做完离线包机制已经一周左右了，因为整个离线包含有H5，App，后台一起协作完成，下面这里用到的技术的总结。

使用的技术点: 

1. **常驻线程** 
2. **多线程(Operation)**
3. **JSBridge**
4. **文件管理**
5. **压缩解压缩**
6. **文件下载**

整体流程:

1. **H5发布离线包**
2. **App下载离线包到本地**
3. **离线包平台[发版/回滚/禁用]**

因为我们的Job是做`APP`的，所以我们会着重说`APP`相关的流程。


流程:
=======
> 下面是我们的离线包，在App的生命周期内的运作。

对于App来说，总体来说可以分两步 

**第一步:** 下载离线包到指定的本地位置，这里我用一张图片说明，我本地下载完的路径是什么样的。 

我们去服务器上传的参数和返回的参数是什么呢? 下面的数据结构展示了App是如何与服务器端进行交互和逻辑处理的。

**具体下载流程:**  详细操作
1. 首先APP启动的时候会开启常驻线程，每间隔一段时间会去服务器请求一次，来更新新包，下面就是`入参`和`出参`，服务器可以下发不允许使用离线包来停用它。

2. 当无本地包的时候，会启用在线包来进行容错，算是个兜底方案。

3. App上架的时候的ipa中是没有离线包的，都是通过网络下载的，因为防止下载包过大。

上传参数数据格式!
```JSON
{
    "data":{
        "appCode":"SomeAppName", //APP标识 与服务器 商定
        "appVer":"1.2.0", //APP 版本 
        "platform":"ios", //手机平台
        "h5vers":[ //H5版本列表
            {
                "moduleId":"10010", // 业务编号
                "moduleVer":"1.0.4", // 版本
                "isActive": true // 是否启用离线
            },
            {
                "moduleId":"10020",
                "moduleVer":"1.0.2",
                "isActive":1
            },
            {
                "moduleId":"10030",
                "moduleVer":"1.0.8",
                "isActive":3
            }
        ]
    }
}
```

服务器返回数据格式!
```JSON
{
    "resultData":[
        {
            "moduleId":"10010", // H5业务号
            "packageUrl":"https://jsoneditoronline.org/aa.zip", // 完整包地址
            "packageMD5":"adfadfadfadfadfadfadfadf", //完整包哈希值
            "patchUrl":"https://jsoneditoronline.org/aa.zip", // 补丁包地址
            "patchMD5":"adfadfadfadfadfadfadfadf", // 补丁包哈希值
            "mode":1, // 下载模式 是完整包 还是补丁
            "isActive":true // 是否启动 离线包
        },
        {
            "moduleId":"10020",
            "packageUrl":"https://jsoneditoronline.org/aa.zip",
            "packageMD5":"adfadfadfadfadfadfadfadf",
            "patchUrl":"https://jsoneditoronline.org/aa.zip",
            "patchMD5":"adfadfadfadfadfadfadfadf",
            "mode":2,
            "isActive":true
        },
        {
            "moduleId":"10030",
            "packageUrl":"https://jsoneditoronline.org/aa.zip",
            "packageMD5":"adfadfadfadfadfadfadfadf",
            "patchUrl":"https://jsoneditoronline.org/aa.zip",
            "patchMD5":"adfadfadfadfadfadfadfadf",
            "mode":1,
            "isActive":false
        }
    ]
}
```



对 下面图片 说明: 首先会去服务器查询可用的离线包，然后拿到下载链接，将离线zip包下载到tmp文件夹下的`FD+UUID`的文件夹下，下面的目录用业务`Key`创建文件名字，里面装着离线H5 Zip包。 只不过图中的 已经被移动到Document文件目录下里了。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/offlinefile1.png" height="150" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/offlinefile2.png" height="150" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/offlinefile3.png" height="150" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/offlinedownload.png" height="350" />


**第二步:**  加载WebView的时候插入离线包机制

具体来说就是我们加载`WebView`的时候判断一下再选择加载，这里我截取一下代码。

```ObjectiveC
- (void)loadUrl: (NSString *)urlString
{
    NSURL *url = [NSURL URLWithString:urlString];

    FDH5OfflineEngine *share = FDH5OfflineEngine.share;
    BOOL isCan = [share isCanAccessOfflineH5WithUrl:url];
    // 是否能加载离线包
    if (isCan) {
        // 将在线的URL转换成离线的URL，这里我其实返回的是一个对象，对象里面包含离线路径和离线路径的根路径
        FDH5OfflineURL *sets = [share onlineUrlConvertToLocalUrlWithUrl:url];
        [self loadFileURL:[sets fileURL] allowingReadAccessToURL:[sets allowingReadAccessToURL]];
    }
    else {
        // 加载在线
        NSURLRequest* request = [NSURLRequest requestWithURL:url cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:15];
        [self loadRequest:request];
    }
}
```
它在App中的整体流程我总结成了一张图如下: 

说明: 当一个业务模块的其中一个方法触发的 scheme跳转(路由实现)，这个scheme是跳转到`WebViewController`的，会在加载`WebView`loadURL的时候做上面的判断再去加载。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/offlineload.png" height="350" />



总结: 
=======
我相信这个方案肯定有些许问题，但是毕竟是我和小伙伴一起想出来并且实现的，并且也成功上线，后续迭代一点点迭代。 这里的iOS技术点，后面会分专门的文章逐一Demo总结。
