---
title: "[Git] fast-forward non-fast-forward 区别"
catalog: true
toc_nav_num: true
date: 2018-11-06 00:06:30
subtitle: "fast-forward non-fast-forward 区别在哪？ 如何使用？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Git
catagories:
- Git
---

案例解释
=======
1. 首先，我们要新增一个功能，所以在`master`上面开一个分支`feature1`
2. 在开发这个`feature1`的时候新增了3个commit
3. 当开发完成的时，`checkout`回`master`，然后`merge`回`feature1`分支，这个时候有两个选择
    
    * 使用 `fast-forward`: 
        ```
        // 当不加 参数 -ff 默认还是 使用的 fast-forward
        git merge --ff feature
        ```
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/fast-forward.png" height="100" />

    * 不使用 `fast-forward(non-fast-forward)`:
        ```
        git merge --no-ff feature
        ```
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/no-fast-forward.png" height="100" />
        
fast-forward
=======
> 这是git默认的执行方式，当用这种方式`merge`时，它不会创建新的`commit`节点来记录这次`merge`，表现形式: 源分支 `master` ，功能分支 `feature`，当 `feature` 分支MR 到`master`时，会将`commit`节点 同步在`master`的历史上，也就是他们有同样的历史。

我个人认为这种方式其实是对原分支的补充才会选择这种方式更为合理，比如我开一个功能分支 `feature/patch` 它只是用来完善 `master` 的。  

non-fast-forward
=======
> 这种方式会新增一个`commit`来记录MR这次历史。 

我认为这种方式的应用场景是开发一个相对于原分支，这里用`master` 独立的功能，换句话说就是类似不要耦合这种，它是单独的可拆卸的。这样用一个新增的`commit`来记录，比如卸载这个功能也会相对简单。

Reference：  
lemonup：http://lemonup.logdown.com/posts/166352-git-merge-fast-forward-differenc 