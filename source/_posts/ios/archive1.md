---
title: "[iOS] Xcodebuild的使用"
catalog: true
toc_nav_num: true
date: 2019-01-28 10:10:30
subtitle: "如何使用Xcodebuild工具来自动化生成ipa"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

前言
=======

> 在写之前要有几个东西先确定下，几个东西。

**1. buildconfiguration:**  
这是一个非常重要的选项，先说一个用到他的例子，一般项目都有各种环境，在同一个位置会有不同的行为，我们要怎么判断呢，`Xcode`提供了一个`Project`级别的参数就是`BuildConfiguration`，我们可以设置有`N`个`BuildConfiguration`，来在代码里面控制代码的不同行为，下面用个截图来看。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/buildconfiguration.png" height="150" />

当设置好了BuildConfiguration，可以对应设置宏来进行条件编译，[如何进行设置看这篇也许对你有帮助。](https://zcoldwater.github.io/blog/article/ios/podPrecompileMacros/)


**2. target:**  
一个`Project`下可以创建多个`Target`，比如Xcode默认的空模版，就会在`Project`下给你建3个`Target`，**1个项目Target**,**1个UnitTest的Target**，**1个UITest对的Target**，共同组建了这个`Project`。  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/target.png" height="150" />

**`作用:`**  
因为一套`Target`对应一套配置，比如我们的项目中，有两个`Target`分别对应不同的`BundleId`，这个场景下，我们就有必要用两个`Target`来区分一下。 再者说两套配置不一样，一套给生产一套给其他环境，里面包含不同的加固配置等等，都可以用多个Target区分。

**3. scheme:**  
一个`Target`可以对应多个`Scheme`，当你创建`Scheme`的时候，是可以选择你的`Target`的，当创建`Scheme`的时候还有一个选项，`Shared`，当你创建的`Scheme`需要其他人也能看到的时候需要打勾，如果不需要其他人看到 不需要打勾。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/scheme.png" height="150" />

**`作用:`**  
目前还没有实际的应用，一般都是一套`Scheme`映射一个`Target`就满足需求了。



**4. 关系:**

> 下图是他们的Scope的关系图。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/projectrelationship.png" height="150" />

从图中可以看出，1个`Project`可以配置多个`Target`，1个`Target`可以配置多个`Scheme`。

`BuildConfiguration`是在`Project`级别上的配置，所以他对下面所有`Target`都生效 。

核心命令
=======

> 执行下面命令的时候`请确保你的证书和你的ID都是正确的`。

**xcodebuild archive:**  
xcodebuild -workspace `<Workspace名字>` -scheme `<Scheme名字>` -configuration `<BuildConfiguration名字>` -archivePath `<导出路径>` archive

**xcodebuild export:**  
xcodebuild -exportArchive -archivePath `<Archive路径>` -exportPath `<Export路径>` -exportOptionsPlist `<ExportOptionsPlist文件路径>`

解释:  

第一步: 先执行`XcodeBuild Archive`命令，生成`.xcarchive`文件。  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/xcarchive.png" height="150" />


第二步: 再执行`XcodeBuild Export`命令，有一个选项 `archivePath`，将第一步生成的`xcarchive`文件路径输入进去，还有一个注意的是，`exportOptionsPlist`，这个参数文件，就是我们平时使用`Xcode`打包出来的文件夹里面包含的文件，表明你导出这个ipa的具体配置。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/exportoption1.png" height="150" />

把之前成功导出的`ExportOptions.plist`文件路径给上面的命令，就可以导出和上次一模一样的ipa包。

`ExportOptions.plist:`  
记录这你的导出参数，你是`AppStore`还是`Development`还是`AdHoc`等等。 


最后
=======

`In general:`

**Development profile** allows you to test your apps on your physical devices  

**App Store Distribution profile** allows you to sign your apps for App Store distribution  

**Ad Hoc Distribution profile** allows you to sign your apps for Ad Hoc distribution  

If you want to test your app with debugger and everything what Xcode provides, you have to use the development profile.  

It is possible to install apps for testing purposes using distribution profiles, but you can't take advantage of the Xcode testing tools.

`Summary:`

Use development profile for testing and debugging your app
Use distribution profile for signing and distribution of your finished and ready-to-use app.
