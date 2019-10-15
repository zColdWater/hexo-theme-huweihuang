---
title: "[iOS] Aspect Hook"
catalog: true
toc_nav_num: true
date: 2019-10-12 14:23:30
subtitle: "AspectHook 使用和原理"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/naked.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境 Xcode11.1 

# 初衷 
最新在看 iOS `Hook` 相关的一些东西，我寻思正好 我就好好看下，把一些常见的库的使用和原理做一个整理，方便以后自己看和其他人查看。

# 使用
> 如何使用 Aspect 库来实现 Hook 一些函数。

首先引入 Aspect 支持Cocoapds安装，添加`pod "Aspects"`即可。

示例代码:
```ObjectiveC
#import "ViewController.h"
#import <Aspects/Aspects.h>

@interface ViewController ()
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 下面只是官方提供的一个demo例子 https://github.com/steipete/Aspects
    // API其实很简单，这里我们使用需要注意几点:
    // 1. aspect_hookSelector 这个函数对 NSObject 基类做了分类拓展，支持 实例方法 和 类方法 。
    // 2. withOptions 参数有
    // ^ AspectPositionAfter   = 0,            /// Called after the original implementation (default) 原函数先调用，Hook函数后调用。
    // ^ AspectPositionInstead = 1,            /// Will replace the original implementation. Hook函数代替原函数，意味着选择这个参数。
    // ^ AspectPositionBefore  = 2,            /// Called before the original implementation. 原函数后调用，Hook函数先调用。
    // ^ AspectOptionAutomaticRemoval = 1 << 3 /// Will remove the hook after the first execution. 只对目标函数Hook一次。
    // 3. usingBlock 参数有 1.id<AspectInfo> + 2.你Hook的函数参数，比如你Hook的viewWillAppear函数，有BOOL类型的Animation参数，所以
    // usingBlock 也要带上这个BOOL参数。
    // 4. **** Aspect 明确说明了，它不支持对静态函数的Hook，也就是我们的类方法 ****
    // 虽然我们可以用 ViewController类 和 ViewController实例 都可以 Aspect Hook 函数，但是其Hook的函数都是 实例方法 不能Hook到静态方法 下面引入下Aspect注释
    //  @note Hooking static methods is not supported. 当然也实际测试了，确实如此。
    //
    id<AspectToken> token = [ViewController aspect_hookSelector:@selector(viewWillAppear:) withOptions:AspectPositionAfter usingBlock:^(id<AspectInfo> aspectInfo, BOOL animation) {
        NSLog(@"hook viewWillAppear");
    } error:NULL];
    
    
    // 移除对目标函数的Hook
//    [token remove];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    NSLog(@"viewWillAppear");
}


@end
```



# 原理
说实话，我一开始是想看别人的原理解读，再来自己看原理的，后来发现，大部分文章太啰嗦，讲了一堆没有说核心部分，我想问了以后我自己再看不懵逼，也为了帮助别人去了解，我准备用图解的方式，来表达核心实现原理。











