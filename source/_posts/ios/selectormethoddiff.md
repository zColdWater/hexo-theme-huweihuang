---
title: "[iOS] Method 和 Selector 区别?"
catalog: true
toc_nav_num: true
date: 2018-11-08 19:19:30
subtitle: "What's the difference between a method and a selector?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/people-diff-min.png"
tags:
- iOS
catagories:
- iOS
---

选择器 Selector
=======
选择器是方法的名称。你很熟悉这些选择：`alloc`，`init`，`release`，`dictionaryWithObjectsAndKeys:`，`setObject:forKey:`，等。注意，冒号是选择的一部分; 这就是我们如何确定这种方法需要参数的方法。另外（虽然它非常罕见），你可以选择这样的选择器：`doFoo:::`。这是一个接受三个参数的方法，你可以像调用它一样调用它`[someObject doFoo:arg1 :arg2 :arg3]`。不要求在选择器组件的每个部分之前都有字母。正如我所说，这是非常罕见的，你不会发现它在Cocoa框架中使用。您可以直接在Cocoa中使用选择器。他们有类型SEL：  `SEL aSelector = @selector(doSomething:)或SEL aSelector = NSSelectorFromString(@"doSomething:")`;

消息 Message
=======
消息是选择器以及您随之发送的参数。如果我说`[dictionary setObject:obj forKey:key]`，那么“消息”是选择器`setObject:forKey:`加上参数`obj`和`key`。可以将消息封装在NSInvocation对象中以供以后调用。消息被发送到接收器。（即，“接收”消息的对象）。

方法 Method
=======
方法是选择器和实现（以及附带的元数据）的组合。“实现”是实际的代码块; 它是一个函数指针`（an IMP）`。可以使用`Methodstruct`（可从运行时检索）在内部检索实际方法。


方法签名 MethodSignature
=======
方法签名表示方法返回和接受的数据类型。它们可以在运行时通过`NSMethodSignature`和（在某些情况下）原始表示`char*`。

实现 Implement
=======
方法的实际可执行代码。它在运行时的类型是一个`IMP`，它实际上只是一个函数指针。iOS 4.3包含了将块转换为块的新功能`IMP`。这真的很酷。  
要实现的一个有趣的事情是方法的名称（选择器）不同于方法的实现`（IMP）`。这意味着如果你感觉大胆，你可以交换它们。您还可以在运行时添加和删除方法，因为您所做的只是编辑哈希表中的条目：键是选择器，值是`IMP`方法的值。这可以让你做一些非常疯狂和冒险的事情。这不适合胆小的人。:)


Reference：   
Stackoverflow:  https://stackoverflow.com/questions/5608476/whats-the-difference-between-a-method-and-a-selector


---