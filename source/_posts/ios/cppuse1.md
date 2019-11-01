---
title: "[iOS] 在iOS项目中使用C++定义的模块"
catalog: true
toc_nav_num: true
date: 2019-01-07 12:55:30
subtitle: "如何在iOS项目中使用C++模块。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo下载: https://github.com/zColdWater/CppBridgeDemo

前言
=======

> 这篇文章就是简单来告诉大家如何引用一个C++的类对象在iOS项目中使用。

下面的文章，我们就以定义一个C++的类在iOS OC环境中使用, 为了方便和直白我们会以图片的形式给大家展示，并且放上Demo。 


预先掌握知识
=======
`.h:` 头文件。头文件包含`类`，`类型`，`函数`和`常数`的声明。  

`.m:` `源代码文件`。这是典型的源代码文件扩展名，可以包含`Objective-C`和`C`代码。  

`.mm:` 源代码文件。带有这种扩展名的源代码文件，除了可以包含`Objective-C`和`C`代码以外还可以包含`C++`代码。仅在你的`Objective-C`代码中确实需要使用`C++`类或者特性的时候才用这种扩展名  

`.cpp:` 只能编译C++当你需要在源代码中包含头文件的时候，你可以使用标准的`#include`编译选项，但是`Objective-C`提供了更好的方法。


具体步骤
=======

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc1.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc2.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc3.png" height="300" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc4.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc5.png" height="300" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc6.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc7.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc8.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc9.png" height="250" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cppoc10.png" height="350" />

