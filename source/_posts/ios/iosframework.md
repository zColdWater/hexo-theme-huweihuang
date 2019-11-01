---
title: "[iOS] Framework"
catalog: true
toc_nav_num: true
date: 2019-01-28 10:10:30
subtitle: ""
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---

# iOS 静态库与动态库

## 知识准备  

这里我们来复习下 C 语言的基本功，编译和链接：

* 编译 `Compile`: 将我们的源代码文件编译为目标文件；
* 链接 `Link`: 将我们的各种目标文件加上一些第三方库，和系统库链接为可执行文件。

由于某个目标文件的符号 ( 可以理解为变量，函数等 ) 可能来自其他目标文件，其实链接 Link 这一步最主要的操作就是 决议符号的地址。

* 若符号来⾃静态库 ( 本质就是 .o 的集合包 )，将其纳⼊链接产物，并确定符号地址。  
* 若符号来⾃动态库，打个标记，等启动的时候再说 --- 交给 dyld 去加载和链接符号。

相关链接：

[动态链接过程](https://blog.cnbluebox.com/blog/2017/10/12/dyld2/)
[Objective-C中的load方法执行的来龙去脉](https://www.jianshu.com/p/c0a1a3ad9336)


## 归类图

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/ios_framework1.png" height="150" />

## 静态库 Static Library  

静态库即静态链接库。之所以叫做静态，是因为静态库在编译的时候会被直接拷贝一份，复制到目标程序里，这段代码在目标程序里就不会再改变了。

静态库的好处很明显，编译完成之后，库文件实际上就没有作用了。目标程序没有外部依赖，直接就可以运行。当然其缺点也很明显，就是会使用目标程序的体积增大。

> 一堆目标文件 ( .o / .obj ) 的打包体   

静态库本质上就是一堆函数的集合，所以把相关文件编译成 .o 文件之后，就可以使用 ar 来把这些文件集合成 .a 文件。

**下面来个简单的例子演示静态库SDK开发流程：**

```shell
//例如我们有两份代码文件，需要封装成静态库。
log.c  memory.c

//把相关文件编译成 .o 文件
➜  clang -c log.c -o log.o
➜  clang -c memory.c -o memory.o

//把 log.o 和 memory.o 塞进 libmylib.a 里面
➜  ar rcs libmylib.a log.o memory.o
```

当你生成好 `libmylib.a` 之后， 编写了一份声明函数接口的头文件 `mylib.h`，一起发给调用方小伙伴；

调用方小伙伴把 `libmylib.a` 和 `mylib.h` 放入路径 `/my/lib/path`；

然后写了个 `demo.c` 调用我们封装好的库，这样把它加进去做编译后的链接：

```shell
//把编译好的 libmylib.a 给 demo.c 链接出，输出最终可执行文件 demo.run
➜  clang -o demo.run demo.c -lmylib -L/my/lib/path
```

当然实际情况没有这么简单，我们 `iOS` 项目中的库实际上是多种硬件框架`（arm64, i386, x84_64 等）`编译结果的集合，这种库叫 `Universal Binary / Fat Library`。

可以用过 `file` 命令查看：

```

➜  file XXXXXXSDK 

XXXXXXSDK: Mach-O universal binary with 5 architectures: [arm_v7:current ar archive] [arm64]
XXXXXXSDK (for architecture armv7): current ar archive
XXXXXXSDK (for architecture armv7s):    current ar archive
XXXXXXSDK (for architecture i386):  current ar archive
XXXXXXSDK (for architecture x86_64):    current ar archive
XXXXXXSDK (for architecture arm64): current ar archive
```

相关链接：

[Mach-O](http://hawk0620.github.io/blog/2018/03/22/study-mach-o-file/)  
[趣探 Mach-O：FishHook 解析](https://www.jianshu.com/p/9e1f4d771e35)   
[程序春秋 向Intel迁移!](http://history.programmer.com.cn/category/%E7%A8%8B%E5%BA%8F%E6%98%A5%E7%A7%8B/)   


## 动态库 Dynamic Library

### 1. Dynamic Framework

动态库即动态链接库。与静态库相反，动态库在编译时并不会被拷贝到目标程序中，目标程序中只会存储指向动态库的引用。等到程序运行时，动态库才会被真正加载进来。

动态库的优点是，不需要拷贝到目标程序中，不会影响目标程序的体积，而且同一份库可以被多个程序使用（因为这个原因，动态库也被称作共享库）。同时，运行时才载入的特性，也可以让我们随时对库进行替换，而不需要重新编译代码。动态库带来的问题主要是，动态载入会带来一部分性能损失，使用动态库也会使得程序依赖于外部环境。如果环境缺少动态库或者库的版本不正确，就会导致程序无法运行。

系统提供的 Framework 都是动态库，比如 UIKit.framework，具有所有动态库的特性。

### 2. Embedded Framework

> 这又是啥？？为什么会有这个东西？

> 我们的苹果爸爸在 iOS 平台上规定不允许存在动态库，并且所有的 IPA 都需要经过苹果爸爸的私钥加密后才能用，基本你用了动态库也会因为签名不对无法加载，(越狱和非 APP store 除外)。于是就把开发者自己开发动态库掐死在幻想中。

> 直到有一天，苹果爸爸的 iOS 升级到了 8，iOS 出现了APP Extension，swift编程语言也诞生了，由于 iOS 主 APP 需要和 Extension 共享代码，Swift 语言的机制也只能有动态库，于是苹果爸爸尴尬了，不过这难不倒我们的苹果爸爸，毕竟我是爸爸，规则是我来定，我想怎样就怎样，于是提出了一个概念Embedded Framework，这种动态库允许APP 和 APP Extension共享代码，但是这份动态库的生命被限定在一个 APP 进程内。简单点可以理解为 被阉割的动态库。

Embedded Framework，这个是用户可以制作的“动态库”，它是受到 iOS 平台限制（签名机制和沙盒机制限制）的动态库，它具有部分动态特性，比如：
* Embedded Framework 可以在 Extension可执行文件 和 APP可执行文件 之间共享，但是不能像系统的动态库一样，在不同的 APP（进程） 中共享
* 系统的 Framework 不需要拷贝到目标程序中，Embedded Framework 最后也还是要拷贝到 APP 中。

> 本质上讲，Embedded Framework 是动态库，他只是我们给动态库起的一个别名！！！  


**Embedded Framework 创建详细过程：**  

参考链接：[Xcode10制作 framework详细步骤及坑说明](https://www.jianshu.com/p/2d2db0d5e9e9)


**Xcode创建的静态库和动态度二者使用过程中的差异比较**
* Xcode 配置

`Embedded Framework` 需要额外的在 `Xcode` 中进行动态库配置，添加到 `Embedded Binraries`内。否则会出现 `dyld: Library not loaded` 错误。
* `ipa` 包中表现

二者在 ipa 包中表现也不一致。  

静态库和源代码一起，打成一个二进制文件 FrameworkDemo；  
  
而动态库单独放在一个文件夹 `Frameworks` 中：

```s
➜  FrameworkDemo.app tree
.
├── Base.lproj
│   ├── LaunchScreen.storyboardc
│   └── Main.storyboardc
├── FrameworkDemo //静态库和源码一起被打成 APP 主体的二进制文件了
├── Frameworks
│   └── EmbeddedFramework.framework //动态库单独放在 Frameworks 文件夹下
│       ├── EmbeddedFramework
│       ├── Info.plist
│       └── _CodeSignature
│           └── CodeResources
├── Info.plist
├── PkgInfo
└── _CodeSignature
    └── CodeResources

7 directories, 13 files
```


**动态库和静态库的判断 -- file 工具**  


动态库
```s
➜  EmbeddedFramework.framework git:(master) ✗ file EmbeddedFramework
EmbeddedFramework: Mach-O 64-bit dynamically linked shared library x86_64
```

静态库
```s
➜  StaticFramework.framework git:(master) ✗ file StaticFramework
StaticFramework: current ar archive random library
```

Framework
```
在 `Mac OS` / `iOS` 平台，`.Framework` 仅仅是一种打包方式。和静态库动态库没有直接关系。
```


参考：   
本文主要摘取自 http://adolsai.com/index.php/archives/iOS-dynamic-and-static-framework.html 

