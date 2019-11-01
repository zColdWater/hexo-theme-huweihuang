---
title: "[RunLoop] 初级 RunLoop基础"
catalog: true
toc_nav_num: true
date: 2018-12-8 14:50:30
subtitle: "RunLoop是什么，它在iOS系统中起什么用？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

RunLoop概念
=======
一般来讲，一个线程一次只能执行一个任务，执行完成后线程就会退出。如果我们需要一个机制，让线程能随时处理事件但并不退出，通常的代码逻辑是这样的：

``` javascript
function loop() {
    initialize();
    do {
        var message = get_next_message();
        process_message(message);
    } while (message != quit);
}
```

这种模型通常被称作 `Event` `Loop`。 `Event` `Loop` 在很多系统和框架里都有实现，比如 `Node.js` 的事件处理，再比如 `OSX/iOS` 里的 `RunLoop`。实现这种模型的关键点在于：如何管理事件/消息，如何让线程在没有处理消息时休眠以避免资源占用、在有消息到来时立刻被唤醒。

所以，`RunLoop` 实际上就是一个对象，这个对象管理了其需要处理的事件和消息，并提供了一个入口函数来执行上面 Event Loop 的逻辑。线程执行了这个函数后，就会一直处于这个函数内部 ***“接受消息->等待->处理”*** 的循环中，直到这个循环结束（比如传入 quit 的消息），函数返回。

`OSX/iOS` 系统中，提供了两个这样的对象：`NSRunLoop` 和 `CFRunLoopRef`。
`CFRunLoopRef` 是在 `CoreFoundation` 框架内的，它提供了纯 C 函数的 API，所有这些 API 都是线程安全的。
`NSRunLoop` 是基于 `CFRunLoopRef` 的封装，提供了面向对象的 API，但是这些 API 不是线程安全的。

`CFRunLoopRef` 的代码是开源的，你可以在这里 http://opensource.apple.com/tarballs/CF/ 下载到整个 CoreFoundation 的源码来查看。

Swift 开源后，苹果又维护了一个跨平台的 CoreFoundation 版本：https://github.com/apple/swift-corelibs-foundation/，这个版本的源码可能和现有 iOS 系统中的实现略不一样，但更容易编译，而且已经适配了 Linux/Windows。


RunLoop 与线程的关系
=======
首先，iOS 开发中能遇到两个线程对象: `pthread_t` 和 `NSThread`。过去苹果有份文档标明了 `NSThread` 只是 `pthread_t` 的封装，但那份文档已经失效了，现在它们也有可能都是直接包装自最底层的 `mach thread`。苹果并没有提供这两个对象相互转换的接口，但不管怎么样，可以肯定的是 `pthread_t` 和 `NSThread` 是一一对应的。比如，你可以通过 `pthread_main_thread_np()` 或 `[NSThread mainThread]` 来获取主线程；也可以通过 `pthread_self()` 或 `[NSThread currentThread]` 来获取当前线程。`CFRunLoop` 是基于 `pthread` 来管理的。

***苹果不允许直接创建 `RunLoop`***，它只提供了两个自动获取的函数：`CFRunLoopGetMain()` 和 `CFRunLoopGetCurrent()`。 这两个函数内部的逻辑大概是下面这样:  

``` ObjectiveC
/// 全局的Dictionary，key 是 pthread_t， value 是 CFRunLoopRef
static CFMutableDictionaryRef loopsDic;
/// 访问 loopsDic 时的锁
static CFSpinLock_t loopsLock;
 
/// 获取一个 pthread 对应的 RunLoop。
CFRunLoopRef _CFRunLoopGet(pthread_t thread) {
    OSSpinLockLock(&loopsLock);
    
    if (!loopsDic) {
        // 第一次进入时，初始化全局Dic，并先为主线程创建一个 RunLoop。
        loopsDic = CFDictionaryCreateMutable();
        CFRunLoopRef mainLoop = _CFRunLoopCreate();
        CFDictionarySetValue(loopsDic, pthread_main_thread_np(), mainLoop);
    }
    
    /// 直接从 Dictionary 里获取。
    CFRunLoopRef loop = CFDictionaryGetValue(loopsDic, thread));
    
    if (!loop) {
        /// 取不到时，创建一个
        loop = _CFRunLoopCreate();
        CFDictionarySetValue(loopsDic, thread, loop);
        /// 注册一个回调，当线程销毁时，顺便也销毁其对应的 RunLoop。
        _CFSetTSD(..., thread, loop, __CFFinalizeRunLoop);
    }
    
    OSSpinLockUnLock(&loopsLock);
    return loop;
}
 
CFRunLoopRef CFRunLoopGetMain() {
    return _CFRunLoopGet(pthread_main_thread_np());
}
 
CFRunLoopRef CFRunLoopGetCurrent() {
    return _CFRunLoopGet(pthread_self());
}
```
从上面的代码可以看出，线程和 `RunLoop` 之间是一一对应的，其关系是保存在一个全局的 Dictionary 里。***线程刚创建时并没有 `RunLoop`，如果你不主动获取，那它一直都不会有。RunLoop 的创建是发生在第一次获取时，RunLoop 的销毁是发生在线程结束时***。你只能在一个线程的内部获取其 `RunLoop`（`主线程除外`）。

总结
=======
1. `RunLoop`是为了保持线程存活而生的，没有`RunLoop`的线程当执行完任务就会退出。  
2. 在iOS当中，`MainThread`默认`RunLoop`是开启的，平时无任务处于休眠状态，一旦接收到其他线程回调，`RunLoop`被唤醒，开始执行任务。  
3. iOS底层是用`Mach`框架进行线程信息的传递的，比如操作系统，内核线程有消息需要告诉当前App的主线程，那么操作系统就会发送`Message`通过`Mach`框架，App的MainThread的`RunLoop`一直监听 `Mach Port`，一旦有消息，就会唤醒MainThread，来处理收到的消息。
4. iOS中，主线线程默认`RunLoop`开启，子线程`RunLoop`默认不会创建，开发者不能直接创建`RunLoop`，需要在线程的上下文中获取（在子线程调用 `RunLoop.current`，如果子线程没有`RunLoop`就会创建一个`RunLoop`）

    ```Swift
    let queue = DispatchQueue(label: "SomeQueue")
    queue.async {
        let currentThread = Thread.current
        currentThread.name = "常驻后台线程"
        // 当前子线程 如果没有RunLoop会自动创建一个RunLoop
        let runloop = RunLoop.current
        // 一定要 执行run 否则RunLoop不会工作。
        runloop.run()
        print("currentThread:\(currentThread)")
    }
    ```


Reference：  
https://blog.ibireme.com/2015/05/18/runloop/  
https://developer.apple.
com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html
