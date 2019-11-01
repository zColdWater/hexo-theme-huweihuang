---
title: "[Git] Cherry Pick"
catalog: true
toc_nav_num: true
date: 2019-05-29 21:42:30
subtitle: "如何使用Cherry Pick?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

前言
=======
Git Cherry Pick 是干什么的？ 我们平时如何使用呢？

Cherry Pick
=======
> 一句话描述: 选择 一个 或者 多个 节点(commit) 合并到当前节点(commit)，如果把每个节点想象成一个一个的樱桃，那么整个操作就好像是挑选樱桃到自己的篮子里(把各个节点变更合到自己的当前节点)

官方文档: https://git-scm.com/docs/git-cherry-pick 

下面是怎么用?

合并branch的最新commit节点/合并指定commit节点  
`git cherry-pick <branch_name>`  
`git cherry-pick <commit_id>`

合并一个范围，开始节点 结束节点  左开右闭 区间
`git cherry-pick <start_commit_id>..<end_commit_id>`

合并一个范围，开始节点 结束节点  左闭右闭 区间  
`git cherry-pick <start_commit_id>^..<end_commit_id>`


```git
单独合并一个提交 如果是分支名字，则合并那个分支的最新的节点
当你`cherry pick`上次合完的节点，因为没有不同的内容 需要执行 `git commit --allow-empty` 或者 执行 `git status` 根据提示操作
git cherry-pick <commit_id/branch_name>

同上，不同点：保留原提交者信息。
git cherry-pick  -x <commit_id>


Git从1.7.2版本开始支持批量cherry-pick，就是一次可以cherry-pick一个区间的commit。

git cherry-pick <start-commit-id>..<end-commit-id> `区间 (start-commit-id end-commit-id] 左开右闭`

git cherry-pick <start-commit-id>^..<end-commit-id> `[start-commit-id end-commit-id] 左闭右闭`

```

在这个过程中需要做什么?  
> 在cherry pick 一个或者多个commit的时候，也许会有冲突，这个时候就需要我们解决冲突，然后添加到暂存，这个时候我们要选择 我们是  `git cherry-pick --continue` 还是 `git cherry-pick --quit` 还是 `git cherry-pick --abort`。 
```
git cherry-pick --continue 继续 完成这次cherry pick的操作，如果cherry pick多个节点，合并每个节点，每次发生冲突 都需要，修改完成， 执行 `git cherry-pick --continue` 来进下一次的commit合并。
git cherry-pick --quit 中途退出 比如你cherry-pick了多个节点，合到中间的一个节点冲突了，解决完，后面的节点你不想再合了，你就可以用这个参数 退出。 
git cherry-pick --abort 还原成未`cherry pick`的样子 放弃 cherry pick 操作，比如你还是cherry-pick了多个节点，但是你觉得我可能不要这样操作了，但是我之前已经 cherry pick了一些节点了，那么我想还原到我之前一开始执行cherry pick的地方，我会使用 --abort
```

PS: 当cherry pick 多个commit 或者一个 区间commit，`你可以想象这些commit是一个数组，你当前HEAD节点，挨个与这些节点进行合并`，每次发生冲突就需要解决冲突 执行 上面 `--continue` , `--quit` , `--abort` 这些命令来控制是否继续，还是中途停止，还是恢复原样。


什么时候使用?
=======
1. 当你需要去合并单个节点的时候，或者多个节点的更新的时候，可以选择使用`cherry-pick`  
   
2. 当你`revert`一些`commit`后，但是其中某一些`commit`需要再被合并进来的时候 可以选择`cherry-pick`，比如 A B C 分支，B分支合并了C分支，然后A分支合并了B分支。 B C 分支都是从A分支的同一个节点出来的，然后我Revert了B的Merge，但是现在我想直接合C的更新，但是因为我Revert了B，相当于间接对C也做了undo操作，所以这个时候 我需要 `cherry-pick`C分支节点到A分支上来。


