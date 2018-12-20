---
title: "[iOS] Operation的简单使用"
catalog: true
toc_nav_num: true
date: 2018-12-20 20:50:30
subtitle: "如何使用Operation呢？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legs.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo下载: https://github.com/zColdWater/OperationDemo 

前言
=======
> 之前的工作中一直没有怎么接触Operation来管理多线程，直到最近在做项目的时候需要使用多线程队列等复杂的任务管理，我开始逐步接触Operation和使用它带给我的方便。

在使用过程中我也在比对，什么时候应该用GCD合适 什么时候用Operation合适.
因为之前对GCD使用比较多一些所以有些问题能用GCD就用GCD完全没有考虑Operation，直到最近使用Operation带来效益才觉得，有些情况更适合使用Operation来处理。

异步Operation
=======
```Swift
/// 如果是需要异步执行代码
/// 需要覆盖: 1.isAsynchronous(默认是false,需要修改成true. 仅是给外界声明自己是 同步操作还是异步操作，没有什么特殊功能。)
/// 需要覆盖: 2.isExecuting 当开始任务 设置成 Ture，执行完成后 设置成 False
/// 需要覆盖: 3.isFinished 当开始任务 设置成 False，执行完成后 设置成 True
/// 需要覆盖: 4.start 异步代码 编写的位置
/// 注意⚠️:  需要手动的来控制Operation状态，Operation才能结束。
class AsyncOperation: Operation {
    
    override init() {}
    
    var result: String = ""
    
    var taskName: String = ""
    
    override var isAsynchronous: Bool { return true }
    
    private let stateLock = NSLock()
    
    // 一定要写KVO 否则无法更改状态
    // willChangeValue && didChangeValue
    private var _executing: Bool = false
    override private(set) var isExecuting: Bool {
        get {
            return stateLock.withCriticalScope { _executing }
        }
        set {
            willChangeValue(forKey: "isExecuting")
            stateLock.withCriticalScope {
                if _executing != newValue {
                    _executing = newValue
                }
            }
            didChangeValue(forKey: "isExecuting")
        }
    }
    
    // 一定要写KVO 否则无法更改状态
    // willChangeValue && didChangeValue
    private var _finished: Bool = false
    override private(set) var isFinished: Bool {
        get {
            return stateLock.withCriticalScope { _finished }
        }
        set {
            willChangeValue(forKey: "isFinished")
            stateLock.withCriticalScope {
                if _finished != newValue {
                    _finished = newValue
                }
            }
            didChangeValue(forKey: "isFinished")
        }
    }
    
    override func start() {
        launchOperation()
        let queue: DispatchQueue = DispatchQueue(label: "serialQueue")
        queue.async {
            // 当前线程睡10s
            sleep(10)
            
            // 结果赋值
            if self.taskName == "任务1" {
                self.result = "I"
            }
            if self.taskName == "任务2" {
                self.result = "Love"
            }
            if self.taskName == "任务3" {
                self.result = "U"
            }
            
            // 改变Operation状态告诉外界，我执行完了，结束了。
            self.completeOperation()
        }
    }
    
    func completeOperation() {
        isExecuting = false
        isFinished = true
    }
    
    func launchOperation() {
        isExecuting = true
        isFinished = false
    }
}

extension NSLock {
    func withCriticalScope<T>(block: () -> T) -> T {
        lock()
        let value = block()
        unlock()
        return value
    }
}

```


同步Operation
=======
```Swift
/// 如果是需要执行同步代码，只需要重写 main() 方法即可，
/// main() 填写同步的方法
/// 注意⚠️:  不需要手动控制自己的状态。
class SyncOperation: Operation {
    
    var result: String = ""
    var taskName: String = ""
    
    override func main() {
        
        if isCancelled { return }
        // 同步睡10s
        sleep(10)
        
        // 结果赋值
        if self.taskName == "任务1" {
            self.result = "I"
        }
        if self.taskName == "任务2" {
            self.result = "Love"
        }
        if self.taskName == "任务3" {
            self.result = "U"
        }
        
    }
    
}
```

需求实现
=======
通过对`同步操作`和`异步操作`的封装得到:

1. 【任务1】输出 'I' [异步Operation]

2. 【任务2】输出 'Love'  [同步Operation]

3. 【任务3】输出 'U'  [异步Operation]

我们期望输出是 'I Love U'，我们可以让三个任务并行去执行。 

Demo Code
```Swift
import UIKit

class ViewController: UIViewController,UITextFieldDelegate {

    
    @IBOutlet weak var input: UITextField!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        input.delegate = self
        input.text = "1"
    }

    
    @IBAction func onLaunch(_ sender: Any) {

        let operation1: AsyncOperation = AsyncOperation()
        operation1.taskName = "任务1"
        let operation2: SyncOperation = SyncOperation()
        operation2.taskName = "任务2"
        let operation3: AsyncOperation = AsyncOperation()
        operation3.taskName = "任务3"
        
        let operation4 = BlockOperation {
            
            // 如果开启并发大于等于3，最早10s被打印出来
            // 如果并发设置成12，最早也要30s被打印出来
            print(operation1.result + " " + operation2.result + " " + operation3.result + "!!!")
        }
        
        operation4.addDependency(operation1)
        operation4.addDependency(operation2)
        operation4.addDependency(operation3)
        
        let queue = OperationQueue.init()
        // 这里设置成10 是让他们并发去执行。
        queue.maxConcurrentOperationCount = Int(input.text!) ?? 1
        queue.addOperations([operation1,operation2,operation3,operation4], waitUntilFinished: false)
        print("我肯定最早被打印出来!!!")
    }
    
    
    
    func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
        
        let aSet = NSCharacterSet(charactersIn:"0123456789").inverted
        let compSepByCharInSet = string.components(separatedBy: aSet)
        let numberFiltered = compSepByCharInSet.joined(separator: "")
        return string == numberFiltered
    }
}
```


