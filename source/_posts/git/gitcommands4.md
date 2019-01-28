---
title: "[Git] Rebase"
catalog: true
toc_nav_num: true
date: 2019-01-23 10:46:30
subtitle: "如何在git当中使用Rebase来优化git历史?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

一、故事
=======

前言:

当我们已经了解了一些`git`的常用的操作后，在实际项目中会发生这样的事情，小王写了一个`bug`已经合入到`主干`了，在一段时间的迭代后，线上发现了这个问题，这个时候需要紧急`hotfix`这个问题，并且领导说希望保持`主干`的`commit`整洁。

故事开始:

因为这个`bug`已经事很久之前的了，首先小王先找到当初提交这个`bug`的`commit`，然后`checkout`出来一个`hotfix`的分支，为了方便我们叫它`A分支`，现在他在`A分支`修改了这个问题，然后提交了`commit`，这个时候小王想`checkout`到`主干`，使用`git rebase A分支`，将刚才修`bug`的`commit`插入到`主干`的历史里面。

小王这时看下`主干的历史`非常整洁明了了，找到领导回报顺利完成任务。



二、实际操作逻辑
=======

> 前面我们已经通过故事知道了大概，现在我们来实际操作。

首先: 创建一个`git`在文件夹`RebaseDemo`下  

操作1: `git init` (创建git)。  
操作2: `touch WechatCode.txt`（添加个文件，比如叫 `WechatCode.txt`）。  
操作3: `git add .` (添加初始化文件)。
操作4: `git commit -m"init"` (初始化提交)。  
操作5: 操作文件 `WechatCode.text` 故意往这个文件写入一些东西 视为`BUG`。  
操作6: 再基于这个`commit`进行后续的修改，代表没发现这个`BUG`，继续开发。   
操作7: `git checkout -b A xxxxxxx(出BUG的Commit号)`   
操作8: `修改BUG`并且提交提交   
操作9: 切回主干`master`   
操作10: `git rebase A`  
操作11: 处理冲突，然后执行`git rebase --continue`  
操作12: 再查看`git log --graph`

之后就可以看到我们的`git`历史非常整洁好看。


三、实际代码记录
=======

> 上面抽象的说了一下，下面我们用实际的代码 来演示一下并且记录它。

结果我们用图片展示一下:  
  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/rebase1.png" height="100" />


Rebase的时候注意几种命令:  

`git rebase --continue` 当rebase后出现冲突，解决好冲突才能进行调用。  
`git rebase --skip`当有冲突的时候不去解决，可以直接用这个跳过。  
`git rebase --abort`放弃这次rebase的操作回到之前。  

    → cd ~/Desktop
    → mkdir RebaseDemo
    → cd RebaseDemo
    → touch WechatCode.txt
    → git init
    → git add .
    → git commit -m"init"
    → git add .
    → git commit -m"提交 BUG"
    → open .//修改文件
    → git add .
    → git commit -m"基于BUG 再写BUG"
    → git log //查看BUG提交Commit
    → git checkout -b hotfix 8c5dc2e88ababe80e709a752907fa05d32f73978
    → git add .
    → git commit -m"update: 修正BUG"
    → git checkout master //切回主干
    → git rebase hotfix //进行rebase操作 会有下面提示。
        First, rewinding head to replay your work on top of it...
        Applying: 基于BUG 再写BUG
        Using index info to reconstruct a base tree...
        M	WechatCode.txt
        Falling back to patching base and 3-way merge...
        Auto-merging WechatCode.txt
        CONFLICT (content): Merge conflict in WechatCode.txt
        error: Failed to merge in the changes.
        Patch failed at 0001 基于BUG 再写BUG
        The copy of the patch that failed is found in: .git/rebase-apply/patch

        Resolve all conflicts manually, mark them as resolved with
        "git add/rm <conflicted_files>", then run "git rebase --continue".
        You can instead skip this commit: run "git rebase --skip".
        To abort and get back to the state before "git rebase", run "git rebase --abort".
    → git add . //解决冲突
    → git rebase --continue




四、危险
=======

Rebase虽然可以给我们带来干净的历史，但是却带来一个风险也是危险的，就是它改变了历史，当你把你的hotfix的commit插入到主干后，后面的commit也都被更改了。