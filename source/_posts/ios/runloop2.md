---
title: "[RunLoop] 中级 RunLoop内部结构"
catalog: true
toc_nav_num: true
date: 2018-12-8 22:50:30
subtitle: "RunLoop是什么，它在iOS系统中起什么用？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/girl1.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

RunLoop 对外的接口
=======
在 CoreFoundation 里面关于 RunLoop 有5个类:

1. ***CFRunLoopRef***:  

    运行循环，主线程默认开启，子线程需手动开启。  

2. ***CFRunLoopModeRef***:  

    其中 CFRunLoopModeRef 类并没有对外暴露，只是通过 CFRunLoopRef 的接口进行了封装。他们的关系如下:  

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/RunLoop0.png" height="200" />  
    
    一个RunLoop是有N多个Mode的，只是图片大小原因。  
    如果你想看苹果更多Mode: http://iphonedevwiki.net/index.php/CFRunLoop

    一个 RunLoop 包含`若干个` Mode，每个 Mode 又包含`若干个 `Source/Timer/Observer。每次调用 RunLoop 的主函数时，只能指定其中一个 Mode，这个Mode被称作 CurrentMode。如果需要切换 Mode，只能退出 Loop，再重新指定一个 Mode 进入。这样做主要是为了分隔开不同组的 Source/Timer/Observer，让其互不影响。  

3. ***CFRunLoopSourceRef***:  

    CFRunLoopSourceRef 是事件产生的地方。Source有两个版本：Source0 和 Source1。  

    • **Source0** 只包含了一个回调（函数指针），它并不能主动触发事件。使用时，你需要先调用 CFRunLoopSourceSignal(source)，将这个 Source 标记为待处理，然后手动调用 CFRunLoopWakeUp(runloop) 来唤醒 RunLoop，让其处理这个事件。  

    • **Source1** 包含了一个 mach_port 和一个回调（函数指针），被用于通过内核和其他线程相互发送消息。这种 Source 能主动唤醒 RunLoop 的线程，其原理在下面会讲到。  


4. ***CFRunLoopTimerRef***:  

    是基于时间的触发器，它和 NSTimer 是toll-free bridged 的，可以混用。其包含一个时间长度和一个回调（函数指针）。当其加入到 RunLoop 时，RunLoop会注册对应的时间点，当时间点到时，RunLoop会被唤醒以执行那个回调。  

    **另外:** 平时我们在主线程执行的`Timer`，直接就可以工作是因为系统封装了，将它加入到了主线程的`RunLoop`，所以才会工作的。  

    但是一旦我们在子线程重新创建了`RunLoop`要将`Timer`加入到运行中的`RunLoop`中。

    ```Swift
    let queue = DispatchQueue(label: "SomeQueue")
    queue.async {
        let currentThread = Thread.current
        currentThread.name = "常驻后台线程"
        let runloop = RunLoop.current
        let timer = Timer(timeInterval: 1,
                        target: self,
                        selector: #selector(self.run),
                        userInfo: nil,
                        repeats: true)
        runloop.add(timer, forMode: .common)
        runloop.run()
        print("currentThread:\(currentThread)")
    }

    @objc func run() {
        print(Thread.current)
    }
    ```

5. ***CFRunLoopObserverRef***:  
    
     是观察者，每个 Observer 都包含了一个回调（函数指针），当 RunLoop 的状态发生变化时，观察者就能通过回调接受到这个变化。可以观测的时间点有以下几个：

     ``` ObjectiveC
     typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
        kCFRunLoopEntry         = (1UL << 0), // 即将进入Loop
        kCFRunLoopBeforeTimers  = (1UL << 1), // 即将处理 Timer
        kCFRunLoopBeforeSources = (1UL << 2), // 即将处理 Source
        kCFRunLoopBeforeWaiting = (1UL << 5), // 即将进入休眠
        kCFRunLoopAfterWaiting  = (1UL << 6), // 刚从休眠中唤醒
        kCFRunLoopExit          = (1UL << 7), // 即将退出Loop
    };
     ```

     上面的 Source/Timer/Observer 被统称为 mode item，一个 item 可以被同时加入多个 mode。但一个 item 被重复加入同一个 mode 时是不会有效果的。如果一个 mode 中一个 item 都没有，则 RunLoop 会直接退出，可以查看RunLoop 实现源码，不进入循环。


RunLoop 深入了解 Mode
=======
CFRunLoopMode 和 CFRunLoop 的结构大致如下：

```ObjectiveC
struct __CFRunLoopMode {
    CFStringRef _name;            // Mode Name, 例如 @"kCFRunLoopDefaultMode"
    CFMutableSetRef _sources0;    // Set
    CFMutableSetRef _sources1;    // Set
    CFMutableArrayRef _observers; // Array
    CFMutableArrayRef _timers;    // Array
    ...
};
 
struct __CFRunLoop {
    CFMutableSetRef _commonModes;     // Set
    CFMutableSetRef _commonModeItems; // Set<Source/Observer/Timer>
    CFRunLoopModeRef _currentMode;    // Current Runloop Mode
    CFMutableSetRef _modes;           // Set
    ...
};
```

这里有个概念叫 “CommonModes”：一个 Mode 可以将自己标记为”Common”属性（通过将其 ModeName 添加到 RunLoop 的 “commonModes” 中）。每当 RunLoop 的内容发生变化时，RunLoop 都会自动将 _commonModeItems 里的 Source/Observer/Timer 同步到具有 “Common” 标记的所有Mode里。

应用场景举例：主线程的 RunLoop 里有两个预置的 Mode：kCFRunLoopDefaultMode 和 UITrackingRunLoopMode。这两个 Mode 都已经被标记为”Common”属性。DefaultMode 是 App 平时所处的状态，TrackingRunLoopMode 是追踪 ScrollView 滑动时的状态。当你创建一个 Timer 并加到 DefaultMode 时，Timer 会得到重复回调，但此时滑动一个TableView时，RunLoop 会将 mode 切换为 TrackingRunLoopMode，这时 Timer 就不会被回调，并且也不会影响到滑动操作。  

有时你需要一个 Timer，在两个 Mode 中都能得到回调，一种办法就是将这个 Timer 分别加入这两个 Mode。还有一种方式，就是将 Timer 加入到顶层的 RunLoop 的 “commonModeItems” 中。”commonModeItems” 被 RunLoop 自动更新到所有具有”Common”属性的 Mode 里去。  

**所以:**   

    1. 要么将Timer加入到CommonMode下，当切换Mode，系统会将CommonMode下的Timer同步到UITracking的Mode下。

    2. 加入到UITracking的Mode下，不用添加到CommonMode下了。

    3. 假如还有其他的属性是Common的Mode，我们不可能都知道，所以你想让Timmer什么模式下都会正常工作，还是加入到CommonMode模式下是最好的选择。


CFRunLoop对外暴露的管理 Mode 接口只有下面2个:

``` ObjectiveC

CFRunLoopAddCommonMode(CFRunLoopRef runloop, CFStringRef modeName);
CFRunLoopRunInMode(CFStringRef modeName, xxxx);

```  

Mode 暴露的管理 mode item 的接口有下面几个：

``` ObjectiveC

CFRunLoopAddSource(CFRunLoopRef rl, CFRunLoopSourceRef source, CFStringRef modeName);
CFRunLoopAddObserver(CFRunLoopRef rl, CFRunLoopObserverRef observer, CFStringRef modeName);
CFRunLoopAddTimer(CFRunLoopRef rl, CFRunLoopTimerRef timer, CFStringRef mode);
CFRunLoopRemoveSource(CFRunLoopRef rl, CFRunLoopSourceRef source, CFStringRef modeName);
CFRunLoopRemoveObserver(CFRunLoopRef rl, CFRunLoopObserverRef observer, CFStringRef modeName);
CFRunLoopRemoveTimer(CFRunLoopRef rl, CFRunLoopTimerRef timer, CFStringRef mode);

```  

你只能通过 mode name 来操作内部的 mode，当你传入一个新的 mode name 但 RunLoop 内部没有对应 mode 时，RunLoop会自动帮你创建对应的   CFRunLoopModeRef。对于一个 RunLoop 来说，其内部的 mode 只能增加不能删除。  

苹果公开提供的 Mode 有两个：kCFRunLoopDefaultMode (NSDefaultRunLoopMode) 和 UITrackingRunLoopMode，你可以用这两个 Mode Name 来操作其对应的 Mode。

同时苹果还提供了一个操作 Common 标记的字符串：kCFRunLoopCommonModes (NSRunLoopCommonModes)，你可以用这个字符串来操作 Common Items，或标记一个 Mode 为 “Common”。使用时注意区分这个字符串和其他 mode name。  


RunLoop 的内部逻辑
=======
根据苹果在[文档](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW23)里的说明，RunLoop 内部的逻辑大致如下: 

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/RunLoop1.png" height="200" />

其内部代码整理如下 （太长了不想看可以直接跳过去，后面会有说明）：

    /// 用DefaultMode启动
    void CFRunLoopRun(void) {
        CFRunLoopRunSpecific(CFRunLoopGetCurrent(), kCFRunLoopDefaultMode, 1.0e10, false);
    }
    
    /// 用指定的Mode启动，允许设置RunLoop超时时间
    int CFRunLoopRunInMode(CFStringRef modeName, CFTimeInterval seconds, Boolean stopAfterHandle) {
        return CFRunLoopRunSpecific(CFRunLoopGetCurrent(), modeName, seconds, returnAfterSourceHandled);
    }
    
    /// RunLoop的实现
    int CFRunLoopRunSpecific(runloop, modeName, seconds, stopAfterHandle) {
        
        /// 首先根据modeName找到对应mode
        CFRunLoopModeRef currentMode = __CFRunLoopFindMode(runloop, modeName, false);
        /// 如果mode里没有source/timer/observer, 直接返回。
        if (__CFRunLoopModeIsEmpty(currentMode)) return;
        
        /// 1. 通知 Observers: RunLoop 即将进入 loop。
        __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopEntry);
        
        /// 内部函数，进入loop
        __CFRunLoopRun(runloop, currentMode, seconds, returnAfterSourceHandled) {
            
            Boolean sourceHandledThisLoop = NO;
            int retVal = 0;
            do {
    
                /// 2. 通知 Observers: RunLoop 即将触发 Timer 回调。
                __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeTimers);
                /// 3. 通知 Observers: RunLoop 即将触发 Source0 (非port) 回调。
                __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeSources);
                /// 执行被加入的block
                __CFRunLoopDoBlocks(runloop, currentMode);
                
                /// 4. RunLoop 触发 Source0 (非port) 回调。
                sourceHandledThisLoop = __CFRunLoopDoSources0(runloop, currentMode, stopAfterHandle);
                /// 执行被加入的block
                __CFRunLoopDoBlocks(runloop, currentMode);
    
                /// 5. 如果有 Source1 (基于port) 处于 ready 状态，直接处理这个 Source1 然后跳转去处理消息。
                if (__Source0DidDispatchPortLastTime) {
                    Boolean hasMsg = __CFRunLoopServiceMachPort(dispatchPort, &msg)
                    if (hasMsg) goto handle_msg;
                }
                
                /// 通知 Observers: RunLoop 的线程即将进入休眠(sleep)。
                if (!sourceHandledThisLoop) {
                    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeWaiting);
                }
                
                /// 7. 调用 mach_msg 等待接受 mach_port 的消息。线程将进入休眠, 直到被下面某一个事件唤醒。
                /// • 一个基于 port 的Source 的事件。
                /// • 一个 Timer 到时间了
                /// • RunLoop 自身的超时时间到了
                /// • 被其他什么调用者手动唤醒
                __CFRunLoopServiceMachPort(waitSet, &msg, sizeof(msg_buffer), &livePort) {
                    mach_msg(msg, MACH_RCV_MSG, port); // thread wait for receive msg
                }
    
                /// 8. 通知 Observers: RunLoop 的线程刚刚被唤醒了。
                __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopAfterWaiting);
                
                /// 收到消息，处理消息。
                handle_msg:
    
                /// 9.1 如果一个 Timer 到时间了，触发这个Timer的回调。
                if (msg_is_timer) {
                    __CFRunLoopDoTimers(runloop, currentMode, mach_absolute_time())
                } 
    
                /// 9.2 如果有dispatch到main_queue的block，执行block。
                else if (msg_is_dispatch) {
                    __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__(msg);
                } 
    
                /// 9.3 如果一个 Source1 (基于port) 发出事件了，处理这个事件
                else {
                    CFRunLoopSourceRef source1 = __CFRunLoopModeFindSourceForMachPort(runloop, currentMode, livePort);
                    sourceHandledThisLoop = __CFRunLoopDoSource1(runloop, currentMode, source1, msg);
                    if (sourceHandledThisLoop) {
                        mach_msg(reply, MACH_SEND_MSG, reply);
                    }
                }
                
                /// 执行加入到Loop的block
                __CFRunLoopDoBlocks(runloop, currentMode);
                
    
                if (sourceHandledThisLoop && stopAfterHandle) {
                    /// 进入loop时参数说处理完事件就返回。
                    retVal = kCFRunLoopRunHandledSource;
                } else if (timeout) {
                    /// 超出传入参数标记的超时时间了
                    retVal = kCFRunLoopRunTimedOut;
                } else if (__CFRunLoopIsStopped(runloop)) {
                    /// 被外部调用者强制停止了
                    retVal = kCFRunLoopRunStopped;
                } else if (__CFRunLoopModeIsEmpty(runloop, currentMode)) {
                    /// source/timer/observer一个都没有了
                    retVal = kCFRunLoopRunFinished;
                }
                
                /// 如果没超时，mode里没空，loop也没被停止，那继续loop。
            } while (retVal == 0);
        }
        
        /// 10. 通知 Observers: RunLoop 即将退出。
        __CFRunLoopDoObservers(rl, currentMode, kCFRunLoopExit);
    }

可以看到，实际上 RunLoop 就是这样一个函数，其内部是一个 do-while 循环。当你调用 CFRunLoopRun() 时，线程就会一直停留在这个循环里；直到超时或被手动停止，该函数才会返回。

RunLoop 的底层实现
=======
从上面代码可以看到，RunLoop 的核心是基于 mach port 的，其进入休眠时调用的函数是 `mach_msg()`。为了解释这个逻辑，下面稍微介绍一下 OSX/iOS 的系统架构。
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/RunLoop3.png" height="200" />

苹果官方将整个系统大致划分为上述4个层次：  
应用层包括用户能接触到的图形应用，例如 Spotlight、Aqua、  SpringBoard 等。  
应用框架层即开发人员接触到的 Cocoa 等框架。  
核心框架层包括各种核心框架、OpenGL 等内容。  
Darwin 即操作系统的核心，包括系统内核、驱动、Shell 等内容，这一层是开源的，其所有源码都可以在 opensource.apple.com 里找到。  
  
我们在深入看一下 Darwin 这个核心的架构：  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/RunLoop4.png" height="200" />

其中，在硬件层上面的三个组成部分：Mach、BSD、IOKit (还包括一些上面没标注的内容)，共同组成了 XNU 内核。  
XNU 内核的内环被称作 Mach，其作为一个微内核，仅提供了诸如处理器调度、IPC (进程间通信)等非常少量的基础服务。  
BSD 层可以看作围绕 Mach 层的一个外环，其提供了诸如进程管理、文件系统和网络等功能。  
IOKit 层是为设备驱动提供了一个面向对象(C++)的一个框架。  

Mach 本身提供的 API 非常有限，而且苹果也不鼓励使用 Mach 的 API，但是这些API非常基础，如果没有这些API的话，其他任何工作都无法实施。在 Mach 中，所有的东西都是通过自己的对象实现的，进程、线程和虚拟内存都被称为”对象”。和其他架构不同， Mach 的对象间不能直接调用，只能通过消息传递的方式实现对象间的通信。”消息”是 Mach 中最基础的概念，消息在两个端口 (port) 之间传递，这就是 Mach 的 IPC (进程间通信) 的核心。  

Mach 的消息定义是在 <mach/message.h> 头文件的，很简单：

    typedef struct {
    mach_msg_header_t header;
    mach_msg_body_t body;
    } mach_msg_base_t;
    
    typedef struct {
    mach_msg_bits_t msgh_bits;
    mach_msg_size_t msgh_size;
    mach_port_t msgh_remote_port;
    mach_port_t msgh_local_port;
    mach_port_name_t msgh_voucher_port;
    mach_msg_id_t msgh_id;
    } mach_msg_header_t;


一条 Mach 消息实际上就是一个二进制数据包 (BLOB)，其头部定义了当前端口 local_port 和目标端口 remote_port，  
发送和接受消息是通过同一个 API 进行的，其 option 标记了消息传递的方向：  

    mach_msg_return_t mach_msg(
                mach_msg_header_t *msg,
                mach_msg_option_t option,
                mach_msg_size_t send_size,
                mach_msg_size_t rcv_size,
                mach_port_name_t rcv_name,
                mach_msg_timeout_t timeout,
                mach_port_name_t notify);


为了实现消息的发送和接收，mach_msg() 函数实际上是调用了一个 Mach 陷阱 (trap)，即函数mach_msg_trap()，陷阱这个概念在 Mach 中等同于系统调用。当你在用户态调用 mach_msg_trap() 时会触发陷阱机制，切换到内核态；内核态中内核实现的 mach_msg() 函数会完成实际的工作，如下图：  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/RunLoop5.png" height="200" />

这些概念可以参考维基百科: [System_call](https://en.wikipedia.org/wiki/System_call)、[Trap_(computing)](https://en.wikipedia.org/wiki/Trap_(computing))。

RunLoop 的核心就是一个 mach_msg() (见上面代码的第7步)，RunLoop 调用这个函数去接收消息，如果没有别人发送 port 消息过来，内核会将线程置于等待状态。例如你在模拟器里跑起一个 iOS 的 App，然后在 App 静止时点击暂停，你会看到主线程调用栈是停留在 mach_msg_trap() 这个地方。

关于具体的如何利用 mach port 发送信息，可以看看 [NSHipster 这一篇文章](https://nshipster.com/inter-process-communication/)，或者这里的[中文翻译](https://segmentfault.com/a/1190000002400329) 。


总结
=======
1. RunLoop内部结构，一个RunLoop对象是由多个Mode组成的，每个Mode又有多个`Timer`、多个`Source`、多个`Observer`组成。
2. CommonMode是一种特殊的Mode，它不是真实的Mode，如果我们把`Timer`,`Source`,`Timer`添加在CommonMode下，当操作系统切换App的RunLoop的Mode时，CommonMode会默认添加到切换到的Mode下面，前提是:切换到的Mode是标记为Common的Mode。
3. RunLoop的实现执行步骤:  
    1. 通知Observer,准备进去RunLoop循环。
    2. 通知Observer,准备处理Timer。
    3. 通知Observer,准备处理Source0。
    4. 处理Source0。
    5. 如果有Source1 跳到第9步骤。
    6. 通知Observer,线程即将休眠。
    7. 休眠等待唤醒。 (Source0, Timer, 外部手动唤醒)
    8. 通知Observer,线程刚被唤醒。
    9. 处理唤醒收到的消息,之后跳会2。
    10. 通知Observer,即将退出RunLoop。
4. RunLoop 线程休眠等待外部唤醒，使用的是底层Mach框架实现。

Reference:
https://blog.ibireme.com/2015/05/18/runloop/  
https://developer.apple.
com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html
