---
title: "[Runtime] NSInvocation"
catalog: true
toc_nav_num: true
date: 2018-11-07 23:46:30
subtitle: "什么是NSInvocation，我们该如何使用它？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- iOS
catagories:
- iOS
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Demo: https://github.com/zColdWater/NSInvocationDemo 

NSInvocation做什么的?
=======
NSInvocation对象用于在对象之间和应用程序之间存储和转发消息，主要是通过NSTimer对象和分布式对象系统。一个NSInvocation对象包含一个Objective-C消息的所有元素：一个目标，一个选择器，参数和返回值。可以直接设置这些元素中的每一个，并在NSInvocation调度对象时自动设置返回值。
一个NSInvocation对象可以被反复派遣到不同的目标; 它的参数可以在发送之间修改以获得不同的结果; 甚至它的选择器也可以使用相同的方法签名（参数和返回类型）更改为另一个。这种灵活性NSInvocation有助于重复具有许多参数和变体的消息; 而不是为每条消息重新输入略有不同的表达式

默认情况下，此类不保留包含的调用的参数。如果这些对象可能在您创建实例NSInvocation的时间和使用它的时间之间消失，那么您应该自己显式保留对象或调用方法以使调用对象自己保留它们。retainArguments

NSInvocation怎么用?
=======
> 这里我上一段Demo，或许你也可以在上面Demo的地址里面找到答案。

为了看下效果，我故意给foo函数包含2个参数，一个返回值。 

```ObjectiveC
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 创建 SEL 方法 二选一即可
//    SEL selector = @selector(foo::);
    SEL selector1 = NSSelectorFromString(@"foo::");
    
    //---------------- 开始 获取方法签名 ----------------//
    NSMethodSignature *sign = [self methodSignatureForSelector:selector1];
    NSLog(@"numberOfArguments:%lu",sign.numberOfArguments);
    NSLog(@"frameLength:%lu",sign.frameLength);
    NSLog(@"isOneway:%d",[sign isOneway]);
    NSLog(@"methodReturnType:%s",[sign methodReturnType]);
    NSLog(@"methodReturnLength:%lu",[sign methodReturnLength]);

    
    //---------------- 结束 获取方法签名 ----------------//

    // 通过 方法签名创建 Invocation 对象
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:sign];
    
    // 方法选择器 必传参数
    invocation.selector = selector1;

    
    //---------------- 开始 设置函数参数 ----------------//
    // 备注: 索引0和1分别表示隐藏的参数self和_cmd; 可以使用target和selector方法直接检索这些值。对通常在消息中传递的参数使用索引2和更大。
    // 官方文档: https://developer.apple.com/documentation/foundation/nsinvocation/1437830-getargument?language=objc
    int arg2 = 10;
    [invocation setArgument:&arg2 atIndex:2];
    
    NSString* (^arg3)(NSString *)  = ^NSString* (NSString* va1)
    {
        return va1;
    };
    [invocation setArgument:&arg3 atIndex:3];
    //---------------- 结束 设置函数参数 ----------------//

    // 开始调用
    [invocation invokeWithTarget:self];
    
    
    //---------------- 开始 插入返回值 ----------------//
    NSString *setResult = @"[替换掉方法真正的返回值]";
    [invocation setReturnValue: &setResult];
    //---------------- 结束 插入返回值 ----------------//

    
    
    //---------------- 开始 得到函数返回值 ----------------//
    id result = nil;
    [invocation getReturnValue:&result];
    NSLog(@"方法返回值:%@",result);
    //---------------- 结束 得到函数返回值 ----------------//

    
    
    //---------------- 开始 查看参数 ----------------//
    id _arg0 = nil;
    [invocation getArgument:&_arg0 atIndex:0];
    NSLog(@"arg0:%@",_arg0);

    SEL _arg1 = nil;
    [invocation getArgument:&_arg1 atIndex:1];
    NSLog(@"arg1:%@",NSStringFromSelector(_arg1));
    
    int _arg2;
    [invocation getArgument:&_arg2 atIndex:2];
    NSLog(@"arg2:%d",_arg2);
    
    id _arg3 = nil;
    [invocation getArgument:&_arg3 atIndex:3];
    NSLog(@"arg3:%@",_arg3);
    //---------------- 结束 查看参数 ----------------//

    
//    NSLog(@"<%@:%@:%d>", NSStringFromClass([self class]), NSStringFromSelector(_cmd), __LINE__);

}


//MARK: - 测试函数
- (id)foo: (int) count
                  : (NSString* (^)(NSString *)) block {
    NSLog(@"personalMethod param1:%d, param2:%@",count,block(@"参数2"));
    return @"[真返回值]";
}



@end
```

补充
=======
> 通过类名，创建类对象并且调用其内部方法。该如何实现呢?

1. 创建demo类 `FooObject` 

    FooObject.h 文件
    ``` ObjectiveC
    #import <Foundation/Foundation.h>
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface FooObject : NSObject

    - (instancetype)initWithWebView:(UIView *)view;

    - (void)sayHello;

    @end

    NS_ASSUME_NONNULL_END

    ```
    FooObject.m 文件
    ``` ObjectiveC
    #import "FooObject.h"
    #import <UIKit/UIKit.h>

    @implementation FooObject

    - (instancetype)initWithWebView:(UIView *)view
    {
        self = [super init];
        if(self) {
            NSLog(@"initWithWebView_init: %@", self);
        }
        return self;
    }

    - (void)sayHello {
        NSLog(@"sayHello");
    }

    @end

    ```

2. 在外部通过类名调用 `FooObject` 对象
    > 这里有个插曲 再动态拿这个函数的返回结果的时候Crash了，经过Google找到了解决办法，下面给了答案地址和解释。
    ``` ObjectiveC

    // Invoke method
    id expectInstance = [self dynamicCreateInstance];
    SEL sayhello = NSSelectorFromString(@"sayHello");
    [expectInstance performSelector:sayhello withObject:nil];


    - (id)dynamicCreateInstance {
        
        id fooClass = [NSClassFromString(@"FooObject") alloc];
        SEL fooInitSel = NSSelectorFromString(@"initWithWebView:");
        NSMethodSignature *fooSign = [fooClass methodSignatureForSelector:fooInitSel];
        NSInvocation *fooInvocation = [NSInvocation invocationWithMethodSignature:fooSign];
        fooInvocation.selector = fooInitSel;
        UIView *webview = [[UIView alloc] init];
        [fooInvocation setArgument:&webview atIndex:2];
        [fooInvocation invokeWithTarget:fooClass];
        
        // prevent crash
        // stackoverflow: https://stackoverflow.com/questions/22018272/nsinvocation-returns-value-but-makes-app-crash-with-exc-bad-access
        id __unsafe_unretained tempResultSet;
        [fooInvocation getReturnValue:&tempResultSet];
        id resultSet = tempResultSet;
        
        //    id fooResult = nil;
        //    [fooInvocation getReturnValue:&fooResult];
        //    NSLog(@"FooObject 对象实例:%@",fooResult);
        
        return resultSet;
    }
    ```



Reference：  
Appple Doc：https://developer.apple.com/documentation/foundation/nsinvocation?language=objc  
Stackoverflow:  https://stackoverflow.com/questions/5608476/whats-the-difference-between-a-method-and-a-selector

