---
title: "[Git] Revert"
catalog: true
toc_nav_num: true
date: 2019-05-29 21:09:30
subtitle: "如何使用Revert，为什么使用Revert而不是Reset"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
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

3. Rervet 命令同样有  `--contiune` / `--quit` / `--abort` 同 [Rebase](https://zcoldwater.github.io/blog/article/git/gitcommands4/) 和 [Cherry-Pick](https://zcoldwater.github.io/blog/article/git/gitcommands8/) 的`--contiune` / `--quit` / `--abort`操作一样。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/git_revert.png" height="100" />

上图的代码是没有`revert`过的，我要解释的其实是这个 -m 参数`1`和`2`的区别，首先 我们当前在 master 并且 merge了`abc`分支 

* 如果 -m 参数用1，使用`master`的分支内容，对`abc`分支的更新内容做 取反操作 再提交新节点。

* 如果 -m 参数用2，使用`abc`的分支内容，对`master`分支的更新内容做 取反操作 再提交新节点。

**需要注意的是:**  我们上面的用谁的更新 指的是 在`合并之前` 到 `合并之后` 这段范围，可以很清楚的看到这里面有两个节点，`master`上面的`update` 和 `abc`分支的`update`。 



总结
=======
Revert操作当在Revert单独一个Commit的时候是比较简单的。 `当Revert一个MergeCommit的时候 是需要使用 -m 参数 选择1 或者 2告诉Git 你要Revert谁的内容。 因为Merge是两个节点的融合成一个节点，所以拆开也是要选择你要拆成哪个。` 还有就是 不论是 SourceTree 还是 Fork 图形化界面，目前默认提供的是Revert操作是不能Revert一个MergeCommit的。需要手动调用Git命令在终端当中。

再次前调一下，使用`Rervet`操作是安全的操作，它是会保存Git历史的。 如果你想取消某一次操作，你首先应该想到的是`Rervet`。






