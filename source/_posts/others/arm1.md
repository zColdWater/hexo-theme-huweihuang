---
title: "[汇编] 看懂简单的汇编"
catalog: true
toc_nav_num: true
date: 2019-09-26 11:38:30
subtitle: "这篇文章是一篇记录，也是一篇最最最基础的汇编讲解。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---

> 文章是自己学习汇编基础知识的记录，如有不正确请指正，谢谢各位。 

⚠️️️️️️️️⚠️⚠️ 这篇文章不是非常专业系统的带你学习汇编的文章，这篇文章是对于汇编新手小白，从来没碰过的哥们快速了解，大致能够看懂汇编，遇到不慌张的目的，如果想专业学习汇编的童鞋请直接绕路，因为作者也是现学现卖 ⚠️⚠️⚠️

# 汇编是个啥
汇编语言（英语：assembly language）是一种用于电子计算机、微处理器、微控制器，或其他可编程器件的低级语言。在不同的设备中，汇编语言对应着不同的机器语言指令集。一种汇编语言专用于某种计算机系统结构，而不像许多高级语言，可以在不同系统平台之间移植。

使用汇编语言编写的源代码，然后通过相应的汇编程序将它们转换成可执行的机器代码。这一过程被称为汇编过程。

汇编语言使用助记符（Mnemonics）来代替和表示特定低级机器语言的操作。特定的汇编目标指令集可能会包括特定的操作数。许多汇编程序可以识别代表地址和常量的标签（Label）和符号（Symbols），这样就可以用字符来代表操作数而无需采取写死的方式。普遍地说，每一种特定的汇编语言和其特定的机器语言指令集是一一对应的。

许多汇编程序为程序开发、汇编控制、辅助调试提供了额外的支持机制。有的汇编语言编写工具经常会提供宏，它们也被称为宏汇编器。

现在汇编语言已不像其他大多数的程序设计语言一样被广泛用于程序设计，在今天的实际应用中，它通常被应用在底层硬件操作和高要求的程序优化的场合。驱动程序、嵌入式操作系统和实时运行程序都会需要汇编语言。

**👆以上信息来自维基百科👆**

为了让大家比较形象的了解汇编，我们先看下汇编的代码。  
`.s`文件结尾的，代码就长这个样子，没学过这东西 谁也不知道它写的是个啥，我们接着往下看。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly1.png" height="200" />


# 一定先搞懂下面的

**各位童鞋，如果你现在也和我一样，看到汇编代码一脸懵逼，变量也不知道，命令也不懂，那么一定把下面的这几个东西看懂，如果看我的文章描述不明白，自己去Google也搞明白再看汇编代码才能看的明白哈。**

### CPU指令集有哪些
我们看看维基百科上查到的典型的指令集:  
链接: https://en.wikipedia.org/wiki/Instruction_set_architecture   
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly2.png" height="50" />
如果有兴趣可以点进去看看每个系列的指令集具体包括啥 `X86` `ARM`等等。

👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇👇

好了，**我开始说人话了**，这就一个新手文章搞这些确实没啥卵用，其实在我们的移动开发当中(做手机软件的码农)，比较常用的就是`ARM`和`X86`系列的，`ARM`是真正的手机上跑的指令集，`X86`是模拟器上跑的指令集。

再进一步说话，说句实话对于咱们新手来说，真的，了解`ARM64`和`ARMv7`就可以了，`X86`都没啥必要，毕竟是模拟器，不是真正跑在客户手机上的指令集。

#### 向下兼容
这里需要指明的是，新的指令集是对旧的的指令集有兼容的，比如`ARMV7`就会对`ARMV7`之前的指令集兼容。

#### ARM64
`ARM64`的指令集，比如`ARM64` `ARM64e`是要在设备运行在64位操作系统

#### ARM
`ARM`的指令集，比如`ARMV7` `ARMV7s`是要在设备运行在32位操作系统

#### X86
`X86`的指令集，是要在设备运行在`AMD`和`Intel`的处理器上。

#### 看看编译出来的文件
这里以iOS应用为例，我们看下我们编译出来的可执行文件指令集是怎样的，接入看图环节。


`一: 先看看模拟器下编译的是什么样子`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly3.png" height="200" />  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly4.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly5.png" height="150" />   

从上图可以看出，我们在操作系统12.2以上的版本下，选择模拟器，编译出来的可执行文件，由于我们的操作系统是64位的，所以是`X86_64`  正是我们之前提到的`X86`指令集系列。 

`二: 再看看真机在最低系统版本12.2以上编译的是什么样子`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly6.png" height="200" />  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly4.png" height="150" />  
  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly7.png" height="200" />

从上图可以看出，我们在操作系统12.2以上的版本下，选择真机，编译出来的可执行文件，只有`ARM64`，因为我们的操作系统是64位的，所以是`ARM64` 不是`ARM其他`，`这时有的同学可能会问为什么我的项目编译出来的可执行文件是两种架构呢，请继续往下看，下面就是为什么会有俩中架构。`


`三: 再看看真机在最低系统版本10.0以上编译的是什么样子`  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly11.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly4.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly8.png" height="200" />

从上图可以看出，我们在操作系统`10.0`以上的版本，选择真机进行编译可以看到有两种编译指令分别是`arm64`和`arm_v7`，前者是`64bit`操作系统使用，后者是`32bit`操作系统使用，其实调最低支持操作系统版本可知，当最低系统版本小于`11.0`的时候就会打出`arm64`和`arm_v7`两个指令集的可执行文件，当最低系统版本大于等于`11.0`的时候只会打出`arm64`指令集的可执行文件。`iOS11`操作系统，以及以后的操作系统都是64位操作系统，所以只需要`arm64`就可以了，但是如果你的最低版本选择的是`10.0`，意味着你的包要支持最低`10.0`~`13.0(目前最高)`的版本，其中`10.0 ~ 10.xxx`用的`32位ARM指令集`，`11.0~13.0`用的`64位ARM指令集`，所以我们看到包里面有两种指令集，因为指令集的兼容性，其实你可以`10.0~13.0`都使用`arm64`指令集也可以，看下面的截图。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly12.png" height="200" />  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/assembly8.png" height="200" />

`四: 多个指令集的可执行文件对包大小的影响`  

### 不同指令集下的寄存器
   

### 汇编指令





