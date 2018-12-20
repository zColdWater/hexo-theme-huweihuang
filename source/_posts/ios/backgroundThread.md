---
title: "[iOS] 常驻线程的创建 Swift"
catalog: true
toc_nav_num: true
date: 2018-12-20 23:50:30
subtitle: "如何创建常驻线程?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo: https://github.com/zColdWater/KeepThreadActive

创建常驻线程
=======
这里需要注意的是，子线程中，如果通过GetCurrentRunLoop方法创建RunLoop后，一定要给RunLoop添加Timer/Source/Observer，否则RunLoop会自动退出。

具体要参考:

1. [RunLoop内部结构](https://zcoldwater.github.io/blog/article/ios/runloop2/)

```Swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 开启RunLoop 保活线程
        let thread = Thread(target: self, selector: #selector(lanchRunloop), object: nil)
        thread.start()
        // 再次调用还能执行任务在这个线程
        perform(#selector(foo), on: thread, with: nil, waitUntilDone: false)

        // 不开启RunLoop 线程执行完就退出
        let thread1 = Thread(target: self, selector: #selector(unlanchRunloop), object: nil)
        thread1.start()
        // 再次调用 无法执行 因为子线程默认不会创建RunLoop 所以执行完就会退出。
        perform(#selector(foo1), on: thread1, with: nil, waitUntilDone: false)

    }

    @objc internal func unlanchRunloop() {
        print("unlanchRunloop")
    }
    
    @objc internal func lanchRunloop() {
        autoreleasepool {
            print("lanchRunloop")
            let currentThread: Thread = Thread.current
            currentThread.name = "常驻线程"
            let currentRunLoop: RunLoop = RunLoop.current
            // 这里不一定非要 add NSMachPort
            // 只要是 Timer Observer Source(MachPort) 添加到一种Mode下都可以
            // 如果不添加 Timer Observer Source 那么RunLoop会自动退出，
            // 具体可以看RunLoop源代码
            currentRunLoop.add(NSMachPort(), forMode: .common)
            currentRunLoop.run()
        }
    }
    
    @objc func foo() {
        print("foo!")
    }
    
    @objc func foo1() {
        print("foo1!")
    }

}

// 控制台打印:::::::
// unlanchRunloop
// lanchRunloop
// foo!
// 控制台打印:::::::

```

