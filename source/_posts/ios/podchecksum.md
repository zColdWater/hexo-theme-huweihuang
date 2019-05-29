---
title: "[Pod] 看懂Podfile.lock"
catalog: true
toc_nav_num: true
date: 2019-05-29 14:10:30
subtitle: "如何看懂Podfile.lock文件"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- Pod
catagories:
- Pod
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

前言
=======

其实本来已经不想介绍这个文件了，其实有些经验的同学肯定对这个都非常理解，毕竟在多人开发中，它尤为有用，比如我们公司项目要有30多个人来维护，每几个人分成一个业务组，每个迭代，每个业务组派出一个人向主分支提交代码，如果我们没有很强的版本意识和Git使用技巧，很容易出现事故。


理解Podfile.lock文件
=======

> 打开Podfile.lock文件你会发现如下的几个Key，我来解释下分别是什么意思。

1. `PODS`:
   1. 例如: 下面是一部分内容 意思是: 你的AFNetworking引用的是3.1.0的版本，这个是你应用实际应用的版本，这里要注意的是，Podfile里面的版本区间，执行`pod install`和`pod update`区别，install 不会更新区间版本，update则会更新到可升区间的最大版本。 尤其在多人开发的大项目有之中，如果Podfile里面没有锁死版本，很容易造成版本被动升级。
        ```yaml
        // AFNetworking 是被安装的是3.1.0版本，这是在你的源下面搜到的podspec文件下的版本号，并且包括依赖AFNetworking的依赖。
        PODS:
              - AFNetworking (3.1.0):
                - AFNetworking/NSURLSession (= 3.1.0)
                - AFNetworking/Reachability (= 3.1.0)
                - AFNetworking/Security (= 3.1.0)
                - AFNetworking/Serialization (= 3.1.0)
                - AFNetworking/UIKit (= 3.1.0)
        ```
2.  `DEPENDENCIES`:
    1.  这个Key下面的其实就是你Podfile里面引用的版本，你可以清楚的看到 他们的版本区间，因为后面会有 = < > 比较符号标记，这是因为 它和 Podfile里面引用版本的方式有关，具体可以看Podfile引用版本的细节，可以设置一个版本区间，可以定死版本。

3.  `SPEC REPOS`:
    1.  这个Key是表示 你这个项目有用到源有哪些，有些私有源，有些公有源，例如 默认的 Github cocoapods的源，私有源就是这个源实在私人服务器上并且不对外开放，公有源就是你这个源可以对任何人开发，对不对外开放这个取决于你的源放在哪里，例如我下面的私有源是在公司内部的gitlab中搭建的，对于外面的人来说肯定就是私有源，因为他们不能访问到我公司的git源。
        ```yaml
        SPEC REPOS:
            "git@git.nevint.com:DOMOBILE-FeiDian/DOMOBILE-FeiDian-FDSpec.git": //私有源
                - Alita
            "git@git.nevint.com:ios/nio-lib-repo.git": //私有源
                - CCUtil
            https://github.com/cocoapods/specs.git: // 公有源
                - AFNetworking
                - Aspects
                - Bugly
                - CocoaSecurity
                - DBPrivacyHelper
        ```
4.  `EXTERNAL SOURCES`:
    1. 在Podfile引用的版本使用的git的引用，没有上传到源的这种引用，会被放在 external sources 。 例如: pod 'RNShop', :git => 'git@git.nevint.com:DOMOBILE-FeiDian/do-otd-mer-nioapp-ios.git', :tag => '0.4.1' 像这种引用，它明确了使用哪个git但是，这个pod并没有上传到私有源版本，所以他会在 外部源里面。 如何发布到私有源 或者 公有源 可以去Cocoapods文档自行查找。

        ```yaml
        EXTERNAL SOURCES:
            AutopartsStoreLib:
                :git: "git@git.nevint.com:DOMOBILE-FeiDian/DOIOS-Nioapp-AutopartsStore-Maintenance.git"
                :tag: 0.6.29
            CodePush:
                :path: "../../do-otd-mer-nioapp-rn/node_modules/react-native-code-push"
            Debug:
                :git: "git@git.nevint.com:DOMOBILE-FeiDian/DOIOS-Nioapp-Debug.git"
                :tag: 1.11.8
            DeepLinkKit:
                :git: "git@git.nevint.com:ios/DeepLinkKit.git"
                :tag: 1.2.2.1
            libwebp:
                :git: "git@git.nevint.com:ios-libs/libwebp.git"
                :tag: v0.6.0
        ```
5.  `CHECKOUT OPTIONS:`:
    1. 这个字段，其实是对应 当有些pod库没有在源(公有源/私有源)当中 PS: 比如你在Podfile里面引入版本用的是tag或者commit，下面的内容是 是真正装在你app当中的三方库代码，从哪个git，哪个tag，哪个commit。 比如你现在要确定你的 `AutopartsStoreLib`，`Debug` 具体版本代码从哪里来，那么你就应该看下面的信息来找到具体下载你本地的库的版本 它是从哪里来的。
   
        ```yaml
        CHECKOUT OPTIONS:
            AutopartsStoreLib:
                :git: "git@git.nevint.com:DOMOBILE-FeiDian/DOIOS-Nioapp-AutopartsStore-Maintenance.git"
                :tag: 0.6.29
            Debug:
                :git: "git@git.nevint.com:DOMOBILE-FeiDian/DOIOS-Nioapp-Debug.git"
                :tag: 1.11.8
            DeepLinkKit:
                :git: "git@git.nevint.com:ios/DeepLinkKit.git"
                :tag: 1.2.2.1
            libwebp:
                :git: "git@git.nevint.com:ios-libs/libwebp.git"
                :tag: v0.6.0
        ```
6.  `SPEC CHECKSUMS`:
    1.  这个hash值怎么来的呢，我来解释一下，例如: `Alita: 577f02fd587c7722d9c063c70a278a0ef6f35ef9`，那么这个hash值是对 我们本地`/Users/yongpeng.zhu/.cocoapods/repos/nevint-domobile-feidian-fdspec/Alita/0.2.1/Alita.podspec`这个文件的SHA1的值来保证，你的podspec文件是一致的。 如果这个地方发生了改变，那么只可能是你的podspec文件发生了改变。
        ```yaml
            SPEC CHECKSUMS:
                AFNetworking: 5e0e199f73d8626b11e79750991f5d173d1f8b67
                Alita: 577f02fd587c7722d9c063c70a278a0ef6f35ef9
                Aspects: 7595ba96a6727a58ebcbfc954497fc5d2fdde546
                AutopartsStoreLib: 4663847a6d03364760affc5b4cefda84e1d6d214
                Bugly: 3ca9f255c01025582df26f9222893b383c7e4b4e
                CCUtil: 5c83684e2e5e514fe20a16de0c95a250faf2cf75
        ```
7.  `PODFILE CHECKSUM`:
    1.  这个字段就是对Podfile文件的SHA1的值。用来对比Podfile是否有过改变。


最后
=======
上面我对`Podfile.lock`文件每个字段都介绍了一下，其实它最主要的功能就是告诉你，你当前的app使用的三方库的版本，来源，和他们生成的hash值，用在多人协作中，来确定版本是否被更改等等。 因为这份文件才是 你真正在app当中的三方库版本，而不是Podfile文件里面写的。 `Podfile`更像是一个版本约束，而Podfile.lock才是你真正使用的版本。 如果让你去确定你app使用某一个三方库的版本，你不应该找Podfile，而是应该找Podfile.lock文件。 即使你Podfile使用的定死版本的方式。 



