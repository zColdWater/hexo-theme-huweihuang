---
title: "[iOS] Pod使用宏预编译"
catalog: true
toc_nav_num: true
date: 2018-12-24 14:26:30
subtitle: "如何使用Pod进行宏预编译"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/blond-1866951.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1 

> Demo地址: https://github.com/zColdWater/PodPrecompileMacros

如何在CocoaPods里使用ProcompileMacros
=======
> 当我们为我们的项目设置了各个Configuration，比如像 `Debug`, `Stage`, `Release`这种。 

下面是在我的项目中设置的三种配置: 
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile1.png" height="200" />

然后我们为我们的三种配置 添加他们各自的标识符 
例如添加`DEBUG`为Debug配置, Swift Compiler - Custom Flags选项卡 ,Other Swift Flags这行 添加 "-D DEBUG"

**注意: -DDEBUG=1 不工作. 只有 -D DEBUG 工作**

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile3.jpeg" height="200" />


然后我们回主项目的`Target`里面来看下效果
我们切换Configuration，然后看下面的输出 是否和我们预期的一致。  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile2.png" height="200" />


上面就是配置宏预处理的步骤，但是Pod在集成的时候会被变成一个`Framework`，所以上面的设置需要在`Framework`里面再设置一遍才能对`Framework`里面的代码生效。 

那么Cocoapods也提供了让你去设置的脚本函数，下面就是在Podfile文件里面加的脚本。
**注意: 要加'$(inherited)','-D','COCOAPODS' 因为这是CocoaPods默认的带上的。**
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile4.jpeg" height="200" />


添加之后执行`pod install`后，查看下Pod的配置是否正确。
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile5.jpeg" height="200" />

插入一下，我们的Pod里面的代码是下面这样的
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile6.jpeg" height="200" />

最终我们运行Demo
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/precompile7.jpeg" height="200" />


参考文档: https://www.mokacoding.com/blog/cocoapods-and-custom-build-configurations/