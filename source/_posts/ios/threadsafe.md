---
title: "[iOS] 线程安全"
catalog: true
toc_nav_num: true
date: 2018-12-28 20:34:30
subtitle: "什么情况我们应该注意线程安全？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/piano1.jpg"
tags:
- iOS
catagories:
- iOS
---
> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1  

前言
=======
这篇文章主要是列出什么情况下我们应该注意多线程的线程安全？

概述
=======
如果用一句话来概述什么情况下我们应该注意线程安全的问题。  

**当在多个线程中，同时对一个内存对象进行写操作就会出现问题。** 

这个特别提一种情况，也是提醒自己的作用，下面的例子:
```Swift
class MyTestClass: NSObject {
    
    // 对象一 属性 name
    static var name: String = "default"
    
    // 对象二 函数实现 foo
    static func foo(_ arg: String) {
        print("arg:\(arg)")
    }
    
}

func main {
    let queue = DispatchQueue.global()
    queue.async {
        MyTestClass.name.append("1")
        MyTestClass.foo("*")
    }
    queue.async {
        MyTestClass.name.append("2")
        MyTestClass.foo("**")
    }
    queue.async {
        MyTestClass.name.append("3")
        MyTestClass.foo("***")
    }
    queue.async {
        MyTestClass.name.append("4")
        MyTestClass.foo("****")
    }
    
    DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
        print("MyTestClass.share.name:\(MyTestClass.name)")
    }
}

//        4:default14   Thread:<NSThread: 0x6000014ea080>{number = 4, name = (null)}
//        1:default14   Thread:<NSThread: 0x6000014e5e80>{number = 6, name = (null)}
//        arg:*
//        3:default14   Thread:<NSThread: 0x6000014e5e00>{number = 3, name = (null)}
//        arg:***
//        2:default14   Thread:<NSThread: 0x6000014e5e40>{number = 5, name = (null)}
//        arg:**
//        arg:****
//        MyTestClass.share.name:default14
```  

解释:   
这里我们给 `MyTestClass`这个类添加了一个类属性，又添类方法，然后开多个线程开始操作他们。  

我们会发现给`name`进行写操作会出现问题，看下面打印就知道了，输出了`default14`应该是`1234`都会添加上才对，这里就是在多个线程同时对统一个对象进行写操作，带来的线程不安全现象，明显发现和预期的结果不对应。  
 
那么会有同学疑问，你就不担心`MyTestClass.foo()`这个类方法么？因为在多线程中`MyTestClass.foo`这个方法也可以看作一个对象，只不过，我们事先在编写这个类的时候已经把实现写好了，也就是我们已经对`MyTestClass.foo`完成了`写`操作，我们在多线程中其实是调用`MyTestClass.foo()`其实就是相当于`读`方法，所以不用考虑他的线程安全。


以上内容是在自己的开发中遇到的问题的思考与总结，如果不正确望大家及时指正。


