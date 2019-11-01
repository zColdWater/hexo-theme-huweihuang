---
title: "[iOS] 解读fishhook实现"
catalog: true
toc_nav_num: true
date: 2019-10-15 17:23:30
subtitle: "了解fishhook的实现原理"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> Xcode11.1  Demo: https://github.com/zColdWater/fishhookdemo 

# 写这篇文章背景  
由于公司领导大大看了抖音的二进制符号重排加快启动速度，这个调研和实现的任务就交给我了，由于我之前并没有对编译相关的东西摸的明明白白，看起来确实有点吃力，从底层开始了解，就了解到逆向了，从逆向又到hook，然后就再一次了解到了fishhook，正好看下实现原理，如果以后我忘记了，可以回顾，如果对其他人有帮助，那更好了。

# 使用  


```ObjectiveC

#import <UIKit/UIKit.h>
#import "AppDelegate.h"
#import <fishhook/fishhook.h>

// 声明nslog一样方法签名的匿名函数，并且用 origin_nslog指针 指向。
void (*origin_nslog)(NSString *format, ...);

// my_log用于接替原函数nslog的作用，
// 如果hook成功，每次调用NSLog方法就会先调用到这里。
void my_log(NSString *format, ...) {
    
  // 打印输出 代表勾住了(hook住了)
  printf("NSLog 已经被我 Hook了!!!!!! \n");
  
  // 因为已经hook住了，origin_nslog 指针，指向的就是nslog的实现。
  return origin_nslog(format);
}


int main(int argc, char * argv[]) {
    
    // 声明一个 fishhook 的结构体，这个结构体非常重要，有3个参数。
    //    struct rebinding {
    //      const char *name;   // 你要hook的方法名称
    //      void *replacement;  // 你自己准备的方法用来替换hook的方法
    //      void **replaced;    // 指向被替换下来的原方法
    //    };
    struct rebinding nslog_rebinding = {
        "NSLog",
        my_log,
        (void*)&origin_nslog
    };
    
    // 使用FishHook的API，rebind_symbols 开始重绑定符号去hook方法。
    rebind_symbols((struct rebinding[1]){nslog_rebinding}, 1);
    
    
    // 用来验证是否NSLog被Hook。
    NSLog(@"function main entrance");
    
    NSString * appDelegateClassName;
    @autoreleasepool {
        // Setup code that might create autoreleased objects goes here.
        appDelegateClassName = NSStringFromClass([AppDelegate class]);
    }
    return UIApplicationMain(argc, argv, nil, appDelegateClassName);
}


输出:  
Hook成功!   
2019-10-18 14:46:08.035252+0800 FishhookDemo[75938:4158602] function main entrance   

```

# 注意

这里要说明的是: `Fishhook`只能`hook`系统动态库的`c`方法。  

Q: 为什么会这样呢?  
A: 这要说到它的`hook`的实现原理，简单的说是，我们在项目中自己实现的`c`函数会被打进编译后的文件里面，而系统的动态库的实现不会打进编译后的文件里面，只是留了个记号，告诉程序，你要执行这个，要先按照这个记号去指定的位置就可以找到实现了，而程序执行到这动态去找的这个动作就用到了`DYLD`这个程序了，它也是一个程序，专门做动态加载的。   

我知道我的表达能力不强，我画个示意图，尽可能表达清楚。  



# 核心原理

