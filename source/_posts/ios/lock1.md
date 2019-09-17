---
title: "[iOS] Locker"
catalog: true
toc_nav_num: true
date: 2019-09-16 15:45:30
subtitle: "学习iOS中的那些Locker"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- iOS
catagories:
- iOS
---

Demo: https://github.com/zColdWater/locker 

# iOS中常见的"锁"

> 下面这些方法都有能达到锁线程的功能，对于锁我其实并不太关系它的实现，只要知道它的功能使用场景就可以了。  

* semaphore: 信号量
* nslock: 互斥锁
* conditionLock: 条件锁
* recursiveLock: 递归锁
* pthread_mutex: 互斥锁
* os_unfair_lock: 自旋锁
* condition: 条件锁
* barrier: 栅栏函数

我把市面上大家常用的锁的使用Demo都写了一遍，各个锁有各个锁的使用场景，各不相同，在使用场景相同的情况下再考虑所谓锁的性能，要么真的没啥必要。

# 使用说明

```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        semaphore()
        nslock()
        conditionLock()
        recursiveLock()
        pthread_mutex()
        os_unfair_lock()
        condition1()
        condition2()
        condition3()
        barrier()
    }

    // 互斥锁
    func nslock() {
        // 创建锁
        let locker = NSLock()
        // 主线程队列添加异步任务，其实就是不阻塞当前上下文，将任务添加到队列的最后面执行。
        DispatchQueue.main.async {
            print("NSLOCK: 我一定先执行，因为我在的线程已经上锁，其他线程加锁内容不能比插进来。")
            locker.unlock()
        }
        
        DispatchQueue(label: "label").async { // 子线程队列
            print("NSLOCK: 我是不确定的，因为我是子线程，什么时候执行我全看苹果GCD框架如何安排，有可能早于主线程内容输出哦！")
            // 不论这个子线程是否早于主线程执行，都会在主线程 do something1 之后执行。 因为主线程已经先上锁了。 所以必须等主解锁才可以进来。
            locker.lock()
            print("NSLOCK: 即使我先执行也没卵用，因为主线程已经上锁了，我肯定最后被执行了")
            locker.unlock()
        }
        
        locker.lock()
        
        /**
         Summary
         
         Attempts to acquire a lock and immediately returns a Boolean value that indicates whether the attempt was successful.
         Declaration
         
         func `try`() -> Bool
         Returns
         
         true if the lock was acquired, otherwise false.
         
         尝试去加锁，如果加上了就返回true，没加上就返回false，在这里返回了false，因为在当前线程之前已经lock了，必须unlock过后才可以再次lock。
         */
        locker.try()
        print("NSLOCK: 主线程已经上锁")
    }
    
    // 信号量
    func semaphore() {
        let semaphore = DispatchSemaphore(value: 0)
        
//        DispatchQueue.main.async { // 主线程队列 如果在主队列上异步执行 也会被卡住
//            print("do sth")
//            semaphore.signal()
//        }
        
        DispatchQueue(label: "label").async { // 子线程队列
            print("DispatchSemaphore: 我依然会执行，因为信号量现在卡住的是主线程！")
            semaphore.signal()
            print("DispatchSemaphore: 上面的semaphore.signal代码会解开信号量，使主线程继续 继续往下跑！")
        }
        
        // 执行wait会对信号量总数 -1，这时如果信号量 小于0 则阻塞当前线程，否则继续。
        semaphore.wait()
        print("DispatchSemaphore: 子线程执行完 semaphore.signal() 才会让我执行，否则一直卡住了。")
    }
    
    // 条件锁
    func conditionLock() {
        
        // 初始化条件锁 (当然你也可以给设置初始Condition(Int)) 默认不设置 Condition = 0
        let locker = NSConditionLock()
        // let locker = NSConditionLock.init(condition: 2)
        
        /**
         说明: locker有一个核心属性是 Condition
         locker.lock() 当没有被轮到执行的时候会卡住当前线程 并不设置locker的condition属性
         locker.lock(whenCondition: A) 当没有被轮到执行的时候会卡住当前线程，并且当locker的condition=A的时候才执行
         locker.unlock() 解除卡住当前线程的locker 并不设置locker的condition属性
         locker.unlock(withCondition: B) 解除卡住当前线程的locker 并设置locker的condition属性=B
         
         这里补充一下:
         有一种情况，NSConditionLock初始condition设置成2
         同时有2个lock代码
         1. locker.lock()
         2. locker.lock(whenCondition: 2)
         会执行1 locker.lock()，只有没有1 locker.lock()的情况下，才会执行2。 也就是说 locker.lock() 优先级最大。
        */
        
        /**
         实验步骤:
         由于线程1，2，3都加了锁卡住了线程执行，由于有locker.lock()，所以会优先执行queue1中的代码，
         当执行完成将condition设置成1，这个时候queue2看到condition设置成1，它可以执行了，以此类推
         所以试验结果是 queue1 queue2 queue3 顺序执行
         */
        
        // 线程一
        let queue1 = DispatchQueue.init(label: "thread1")
        queue1.async {
            locker.lock()
            print("NSConditionLock1:\(Thread.current)")
            locker.unlock(withCondition: 1) //
            // locker.unlock() // 如果unlock没有指定condition，那么其他线程lock指定condition的代码将被锁住不会执行。
        }
        
        // 线程二
        let queue2 = DispatchQueue.init(label: "thread2")
        queue2.async {
            locker.lock(whenCondition: 1)
            print("NSConditionLock2:\(Thread.current)")
            locker.unlock(withCondition: 2)
        }
        
        // 线程三
        let queue3 = DispatchQueue.init(label: "thread3")
        queue3.async {
            locker.lock(whenCondition: 2)
            print("NSConditionLock3:\(Thread.current)")
            
            // 设置回初始值 0，当然你也可以设置成你想用的值，这里根据实际情况来，因为我整个流程结束了，所以我把这个对象的属性设置成默认值。
            locker.unlock(withCondition: 0)
        }
        
    }
    
    // 递归锁
    func recursiveLock() {
        
        // 创建锁
        let locker = NSRecursiveLock()
        
        /**
         说明: 递归锁不像互斥锁那样，在同一个线程当中，如果两次lock，前一个没有unlock会阻塞后面的代码执行 例如:
         
         --- 互斥锁 NSLOCK ---
         locker.lock() // 锁住
         locker.lock() // 发现上一个锁没有解锁 就卡住下面的代码
         print("thread1")
         locker.unlock()
         
         --- 递归锁 NSRecursiveLock ---
         locker.lock() // 锁住
         locker.lock() // 发现上一个锁没有解锁，没事继续执行，locker会记录 已经加锁2次了
         print("thread1") // 打印
         locker.unlock() // 解锁1次，因为之前上锁2次，现在只解锁1次肯定不行，其他线程就无法加锁，就被卡住了。
         
         let queue = DispatchQueue(label: "thread2")
         queue.async {
             locker.lock() // 被卡住 因为递归锁 还有1次unlock才可以释放掉上一个线程
             print("thread2")
             locker.unlock()
         }
    
         */
        
        let queue1 = DispatchQueue(label: "thread1")
        queue1.async {
            locker.lock()
            locker.lock()
            print("NSRecursiveLock: 会被执行")
            locker.unlock()
        }
        
        let queue = DispatchQueue(label: "thread2")
        queue.async {
            locker.lock()
            print("NSRecursiveLock: 不会被执行，要上一个线程 unlock次数 = lock次数 才可以")
            locker.unlock()
        }
    }
    
    // 互斥锁 c语言实现的跨平台互斥锁 (猜测NSLOCK是封装的pthread_mutex_t，仅仅是猜测)
    func pthread_mutex() {
        
        // 创建锁
        var mutex = pthread_mutex_t()
        pthread_mutex_init(&mutex, nil)
        
        DispatchQueue.main.async {
            print("NSLOCK: 我一定先执行，因为我在的线程已经上锁，其他线程加锁内容不能比插进来。")
            pthread_mutex_unlock(&mutex)
        }
        
        DispatchQueue(label: "label").async { // 子线程队列
            print("NSLOCK: 我是不确定的，因为我是子线程，什么时候执行我全看苹果GCD框架如何安排，有可能早于主线程内容输出哦！")
            // 不论这个子线程是否早于主线程执行，都会在主线程 do something1 之后执行。 因为主线程已经先上锁了。 所以必须等主解锁才可以进来。
            pthread_mutex_lock(&mutex)
            print("NSLOCK: 即使我先执行也没卵用，因为主线程已经上锁了，我肯定最后被执行了")
            pthread_mutex_unlock(&mutex)
        }
        pthread_mutex_lock(&mutex)
        print("NSLOCK: 主线程已经上锁")
    }
    
    // 自旋锁 由于OSSpinLock已经被废弃掉了,原因是低优先级的线程如果锁住了资源，高优先级的线程访问时会破坏掉锁的资源.
    // 苹果开发 os_unfair_lock是替代OSSpinLock的产物
    // 自旋锁是目前来看这些锁中性能最好的一个
    func os_unfair_lock() {
        var unfairLock = os_unfair_lock_s()
        
        // 主线程队列添加异步任务，其实就是不阻塞当前上下文，将任务添加到队列的最后面执行。
        DispatchQueue.main.async {
            print("NSLOCK: 我一定先执行，因为我在的线程已经上锁，其他线程加锁内容不能比插进来。")
            os_unfair_lock_unlock(&unfairLock)
        }
        
        DispatchQueue(label: "label").async { // 子线程队列
            print("NSLOCK: 我是不确定的，因为我是子线程，什么时候执行我全看苹果GCD框架如何安排，有可能早于主线程内容输出哦！")
            // 不论这个子线程是否早于主线程执行，都会在主线程 do something1 之后执行。 因为主线程已经先上锁了。 所以必须等主解锁才可以进来。
            os_unfair_lock_lock(&unfairLock)
            print("NSLOCK: 即使我先执行也没卵用，因为主线程已经上锁了，我肯定最后被执行了")
            os_unfair_lock_unlock(&unfairLock)
        }
        os_unfair_lock_lock(&unfairLock)
        print("NSLOCK: 主线程已经上锁")
    }
 
    // 条件锁 场景一
    func condition1() {
        let locker = NSCondition()

        /** --- 场景一 当互斥锁使用 --- */
        // 主线程队列添加异步任务，其实就是不阻塞当前上下文，将任务添加到队列的最后面执行。
        DispatchQueue.main.async {
            print("NSLOCK: 我一定先执行，因为我在的线程已经上锁，其他线程加锁内容不能比插进来。")
            locker.unlock()
        }
        
        DispatchQueue(label: "label").async { // 子线程队列
            print("NSLOCK: 我是不确定的，因为我是子线程，什么时候执行我全看苹果GCD框架如何安排，有可能早于主线程内容输出哦！")
            // 不论这个子线程是否早于主线程执行，都会在主线程 do something1 之后执行。 因为主线程已经先上锁了。 所以必须等主解锁才可以进来。
            locker.lock()
            print("NSLOCK: 即使我先执行也没卵用，因为主线程已经上锁了，我肯定最后被执行了")
            locker.unlock()
        }
        locker.lock()
        print("NSLOCK: 主线程已经上锁")
    }
        
    // 条件锁 场景二
    func condition2() {
        let locker = NSCondition()

        /** --- 场景二 阻塞单个上下文线程 --- */
        let queue = DispatchQueue.init(label: "线程1")
        queue.async {
            locker.lock()
            print("NSCondition: 我是A，先执行")
            // 卡住当前线程 直到2s后 自动解开
//            locker.wait(until: Date.init(timeInterval: 2, since: Date()))
            // 一直卡住 直到 locker.broadcast() locker.signal() 才会解开

            /**
             Summary
             Blocks the current thread until the condition is signaled.
             You must lock the receiver prior to calling this method.

             Wait这个方法，功能是阻塞当前线程，直到被通知可以解开。
             注意的是: 调用这个方法的时候只能在Lock的上下文中，也就是只能在 lock 和 unlock 之前调用。
             */
            locker.wait()
            print("NSCondition: 我要等A执行之后才执行，因为线程被卡住了")
            locker.unlock()
        }

        DispatchQueue.main.asyncAfter(deadline: .now() + 2) {

            /**
             Summary
             Signals the condition, waking up one thread waiting on it.
             Discussion
             You use this method to wake up one thread that is waiting on the condition. You may call this method multiple times to wake up multiple threads. If no threads are waiting on the condition, this method does nothing.
             To avoid race conditions, you should invoke this method only while the receiver is locked.

             发送一个信号，去解开 wait方法阻塞的线程，这个方法一次只能解开一个，如果有多个线程被wait你可以调用多次去解开
             为了避免条件竞争，你应该调用这个方法只有当wait的线程在lock和unlock之间 才可以。
             */
            locker.signal()
            /**
             这个我闲麻烦就不贴文档了，就是一次解开所有wait的线程，并且解开的wait线程，wait必须是在 lock 和 unlock 之中调用的。
             */
//            locker.broadcast()
        }
    }

    // 条件锁 场景三
    func condition3() {
        let locker = NSCondition()
        
        /** --- 场景三 阻塞多个上下文线程 --- */
        let queue = DispatchQueue(label: "线程1")
        queue.async {
            locker.lock()
            print("NSCondition: 我是A，先执行")
            // 卡住当前线程 直到2s后 自动解开
            //            locker.wait(until: Date.init(timeInterval: 2, since: Date()))
            // 一直卡住 直到 locker.broadcast() locker.signal() 才会解开
            
            
            /**
             Summary
             Blocks the current thread until the condition is signaled.
             You must lock the receiver prior to calling this method.
             
             Wait这个方法，功能是阻塞当前线程，直到被通知可以解开。
             注意的是: 调用这个方法的时候只能在Lock的上下文中，也就是只能在 lock 和 unlock 之前调用。
             */
            locker.wait()
            print("NSCondition: 我要等A执行之后才执行，因为线程被卡住了")
            locker.unlock()
        }
        
        /** --- 场景三 阻塞多个上下文线程 --- */
        let queue2 = DispatchQueue(label: "线程2")
        queue2.async {
            locker.lock()
            print("NSCondition: 我是B，先执行")
            // 卡住当前线程 直到2s后 自动解开
            //            locker.wait(until: Date.init(timeInterval: 2, since: Date()))
            // 一直卡住 直到 locker.broadcast() locker.signal() 才会解开
            
            /**
             Summary
             Blocks the current thread until the condition is signaled.
             You must lock the receiver prior to calling this method.
             
             Wait这个方法，功能是阻塞当前线程，直到被通知可以解开。
             注意的是: 调用这个方法的时候只能在Lock的上下文中，也就是只能在 lock 和 unlock 之前调用。
             */
            locker.wait()
            print("NSCondition: 我要等B执行之后才执行，因为线程被卡住了")
            locker.unlock()
        }
        
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) { // 延迟2s
            /**
             这个我闲麻烦就不贴文档了，就是一次解开所有wait的线程，并且解开的wait线程，wait必须是在 lock 和 unlock 之中调用的。
             */
            locker.broadcast()
            // 当然你也可以这么写
            // locker.signal()
            // locker.signal()
        }

    }
    
    // 栅栏函数
    func barrier() {
        
        // 创建并发Queue，并发Queue下执行的Async操作会自动有GCD派发多个不同的线程去执行
        let queue = DispatchQueue.init(label: "concurrent", attributes: .concurrent)
        
        queue.async {
            print("我是A:\(Thread.current)")
        }
        queue.async {
            print("我是B:\(Thread.current)")
        }
        
        /**
         barrier 会将并发队列中的任务隔离开 Run一下看下输出就知道了。
         */
        queue.async(flags: .barrier) {
            print("------------隔离线------------")
            print(Thread.current)
            print("------------AB在一起，CD在一起------------")
        }
        
        queue.async {
            print("我是C:\(Thread.current)")
        }
        queue.async {
            print("我是D:\(Thread.current)")
        }
    }

}

```

# 总结

看完上面的“锁”再问你如何把任务在各个线程中按照一定顺序执行你会说有多少种。

1. GCD Group 
2. NSOperation
3. GCD Barrier
4. 串行任务队列 一个一个顺序执行
5. NSLockCondition 条件锁
6. NSCondition 条件锁
   
等等 是不是太多，下回在遇到多线程中处理任务就不会慌张了。
