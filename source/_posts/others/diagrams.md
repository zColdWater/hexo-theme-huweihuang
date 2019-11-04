---
title: "[Diagrams] 如何画一个序列图"
catalog: true
toc_nav_num: true
date: 2019-11-04 16:30:30
subtitle: "我们如何画一个序列图"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Diagrams
catagories:
- Diagrams
---

> 超级多的模版而且好用的在线作图网站: https://online.visual-paradigm.com/cn/ 
> 包含产品原型图，流程图，时序图 等等等等。。。  

# 什么是时序图？  

时序图（Sequence Diagram），亦称为序列图、循序图或顺序图，是一种UML交互图。它通过描述对象之间发送消息的时间顺序显示多个对象之间的动态协作。  

时序图是一个二维图，横轴表示对象，纵轴表示时间，消息在各对象之间横向传递，依照时间顺序纵向排列。


# 时序图的作用是什么？

1、展示对象之间交互的顺序。将交互行为建模为消息传递，通过描述消息是如何在对象间发送和接收的来动态展示对象之间的交互；  

2、相对于其他UML图，时序图更强调交互的时间顺序；   

3、可以直观的描述并发进程。   


# 常用的组成元素有哪些？


## 角色（Actor）

系统角色，可以是人、机器、其他系统、子系统；在时序图中用表示。

## 对象（Object）

（1）对象的三种命名方式

第一种方式包括对象名和类名，例如：直播课时:课时，在时序图中，用“对象：类”表示；    
第二种方式只显示类名，即表示它是一个匿名对象，例如： :课程；在时序图中，用“：类”表示；  
第三种方式只显示对象名不显示类名，例如：讲师；在时序图中，用“对象”表示。  

（2）命名方式的选择

三种命名方式均可，哪种最容易让阅读该时序图的人理解，就选择哪种。

（3）对象的排列顺序

对象的左右顺序并不重要，但是为了作图清晰整洁，通常应遵循以下两个原则：把交互频繁的对象尽可能的靠拢；2.把初始化整个交互活动的对象放置在最左端。

## 生命线（Lifeline）

在时序图中表示为从对象图标向下延伸的一条虚线，表示对象存在的时间。


## 消息（Message）

消息一般分为同步消息（Synchronous Message），异步消息（Asynchronous Message）和返回消息（Return Message）。

消息的发送者把控制传递给消息的接收者，然后停止活动，等待消息的接收者放弃或者返回控制。用来表示同步的意义；   
消息发送者通过消息把信号传递给消息的接收者，然后继续自己的活动，不等待接受者返回消息或者控制。异步消息的接收者和发送者是并发工作的。   
返回消息表示从过程调用返回。   

## 自关联消息

表示方法的自身调用或者一个对象内的一个方法调用另外一个方法。

## 常用的组合片段

组合片段用来解决交互执行的条件和方式，它允许在序列图中直接表示逻辑组件，用于通过指定条件或子进程的应用区域，为任何生命线的任何部分定义特殊条件和子进程。名称及含义如下：

| 操作符        | 解释           |
| ------------- |:-------------:|
| alt      | 备用多个片段：只执行条件为真的片段 |
| opt      | 可选：仅当提供的条件为真时才执行片段。 相当于只有一条迹线的alt。|
| par      | 并行：每个片段并行运行 |
| loop      | 循环：片段可以执行多次，并且防护指示迭代的基础 |
| region      | 关键区域：片段只能有一个线程一次执行它 |
| neg      | 否定：片段显示无效的交互 |
| ref      | 参考：指在另一个图上定义的交互。 绘制框架以覆盖交互中涉及的生命线。 您可以定义参数和返回值。 |
| sd      | 序列图：用于包围整个序列图 |

### 一些序列图例子 

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams1.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams2.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams3.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams4.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams5.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/diagrams6.png" height="200" />

