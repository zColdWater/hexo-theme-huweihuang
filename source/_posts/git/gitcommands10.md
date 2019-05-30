---
title: "[Git] Revert"
catalog: true
toc_nav_num: true
date: 2019-05-29 21:09:30
subtitle: "如何使用Revert，为什么使用Revert而不是Reset"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

前言
=======
> 在开发项目过程中，我们经常会遇到之前一些代码需要撤回，就比如你操作失误，误合了其他分支代码，这个时候你需要撤销，那么这个时候你是要用Revert还是Reset呢？ 


Revert是干嘛的？
=======
Git Revert 是用来undo的操作，说白了就是取反的操作，比如原来你上一个节点是 增加2行代码，那么你对这个commit执行revert操作就是移除2行代码，完全的取反，但是它和Reset有区别，Revert是再生成一个commit对需要Revert的commit进行取反操作，再提交。 这相比于 Git Reset --hard 是更加安全，因为它的历史被全部保留了起来。


Revert怎么使用?
=======

> 使用起来很简单，如下命令:

1. `git revert <commit_id>` 注意: 这个commit不是 Merge的Commit，如果是Merge的Commit 不能够执行需要加 -m 参数


2. `git revert -m 1或者2 <merge_commit_id>` 注意: 这个commit是Merge的Commit 所以需要 添加参数 -m  才可以 `revert` Merge Commit 这里需要解释的是 这个 参数 1或者2 是什么意思，用一张图来解释。







