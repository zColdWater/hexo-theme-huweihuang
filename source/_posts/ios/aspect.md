---
title: "[iOS] 解读AspectHook原理"
catalog: true
toc_nav_num: true
date: 2019-10-12 14:23:30
subtitle: "AspectHook 使用和原理"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
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

这里需要说明的是Aspect维护了一个黑名单的方法是不允许`hook`的，名单如下: ["retain" "release" "autorelease" "forwardInvocation:"]，这个名单下的方法是不允许hook的。

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
说实话，我一开始是想看别人的原理解读，再来自己看原理的，后来发现，大部分文章太啰嗦，讲了一堆没有说核心部分，我想为了以后我自己再看不懵逼，也为了帮助别人去了解，我准备用图解的方式，直接了当的方式来表达核心实现原理。  

这里我来阐明它能工作最核心的原理，其余一些逻辑我就不放在这里了，并且我会附上一个Demo，在这里Demo中必要源码我基本都有注解。


## 未使用 Aspect Hook

这没啥说的正常结构，主要是下面使用Aspect后的结构。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/aspect1.png" height="100" />

## 使用 Aspect Hook  

下图描述的是他的核心功能如何实现的，其实还包括很多铺垫，比如检查方法hook的block方法签名与被hook的方法签名能否兼容，参数类型等，还有一些吧，在我都看完的情况下，如果全部展开会让大家感觉很难看下去，因为文字组织确实需要一些功力的，否则后面自己看都费劲，所以就可核心的展示，剩下注释我都写在Demo中，有兴趣可以看下。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/aspect2.png" height="250" />

步骤 1.1 / 1.2:    

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/aspect3.jpeg" height="250" />

步骤 2.1 / 2.2:  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/aspect4.png" height="250" />



## 核心逻辑 __ASPECTS_ARE_BEING_CALLED__ 方法

```ObjectiveC
// This is the swizzled forwardInvocation: method.
static void __ASPECTS_ARE_BEING_CALLED__(__unsafe_unretained NSObject *self, SEL selector, NSInvocation *invocation) {
    
    // 这里解释一下，为什么会执行到这里。
    // 因为 aspect 替换了原函的实现，换成objc_msgForward，所以再调被hook函数会好触发消息转发流程
    // 又因为 aspect 用 __ASPECTS_ARE_BEING_CALLED__ 替换了 消息转发流程中的 forwardInvocation 函数 
    // 所以就调用到这里面来了
    
    // 断言参数 self invocation 不为空
    NSCParameterAssert(self);
    NSCParameterAssert(invocation);
    
    // 获取 invocation 的SEL方法名
    SEL originalSelector = invocation.selector;
    // 拼接 别名方法 通过 SEL
	SEL aliasSelector = aspect_aliasForSelector(invocation.selector);
    
    // 替换invocation中的调用方法
    invocation.selector = aliasSelector;
    
    // 通过aspect别名方法获取关联对象 AspectsContainer
    AspectsContainer *objectContainer = objc_getAssociatedObject(self, aliasSelector);
    
    // 通过aspect别名方法获取关联对象 AspectsContainer
    // object_getClass(self) 是 self的isa指向的位置，如果 self 是实例 就指向 类，如果 self 是类 就指向 元类。
    AspectsContainer *classContainer = aspect_getContainerForClass(object_getClass(self), aliasSelector);
    
    // 创建 AspectInfo 对象通过 self 和 invocation
    AspectInfo *info = [[AspectInfo alloc] initWithInstance:self invocation:invocation];
    
    // 创建 aspectsToRemove变量 待用
    NSArray *aspectsToRemove = nil;

    
    
    // Before hooks.
    // 从名字就应该可以猜出，这是我们挂的回调，option = before 在原函数执行之前执行。
    aspect_invoke(classContainer.beforeAspects, info);
    aspect_invoke(objectContainer.beforeAspects, info);

    
    // Instead hooks.
    BOOL respondsToAlias = YES;
    if (objectContainer.insteadAspects.count || classContainer.insteadAspects.count) { // 如果 option=instead替换 走这里
        aspect_invoke(classContainer.insteadAspects, info);
        aspect_invoke(objectContainer.insteadAspects, info);
    }else {
        
        // 这里解释一下，这里是调用原函数的实现，如果你看了上面的图解，你就可以完全看懂下面的代码。

        // 获取invoke对象
        Class klass = object_getClass(invocation.target);
        
        // 如果可以响应 别名函数，循环调用。
        // 这里invoke是调用的原函数
        do {
            if ((respondsToAlias = [klass instancesRespondToSelector:aliasSelector])) {
                [invocation invoke];
                break;
            }
        }while (!respondsToAlias && (klass = class_getSuperclass(klass)));
    }

    
    
    // After hooks.
    // 这里是 option = after 的hook 被调用
    aspect_invoke(classContainer.afterAspects, info);
    aspect_invoke(objectContainer.afterAspects, info);

    
    
    // If no hooks are installed, call original implementation (usually to throw an exception)
    // 如果不响应别名函数，调用原函数实现。
    if (!respondsToAlias) {
        
        invocation.selector = originalSelector;
        SEL originalForwardInvocationSEL = NSSelectorFromString(AspectsForwardInvocationSelectorName);
        if ([self respondsToSelector:originalForwardInvocationSEL]) { // 可以响应原函数
            // 用 objc_msgSend 给自己发送 originalForwardInvocationSEL 消息携带参数 invocation
            ((void( *)(id, SEL, NSInvocation *))objc_msgSend)(self, originalForwardInvocationSEL, invocation);
        }else {
            // 无法响应原函数，调用不识别方法。
            [self doesNotRecognizeSelector:invocation.selector];
        }
    }

    // Remove any hooks that are queued for deregistration.
    [aspectsToRemove makeObjectsPerformSelector:@selector(remove)];
}
```



# 总结
希望可以通过图解的形式最直观的给大家展示aspect的实现原理，一些其他细节可以查看本篇文章Demo，Demo中有对其他方法的详细注释。


