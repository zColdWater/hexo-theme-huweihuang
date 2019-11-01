---
title: "[Git] 回滚"
catalog: true
toc_nav_num: true
date: 2019-05-28 03:59:30
subtitle: "使用Git时候如何回滚？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

这里要明确一下概念:  当一个文件 未被gitignore无视的文件，都会加入到git的track里来，git add 将文件加入到暂存区。

1. [回滚]  `git reset <commit>`
   1. 例子: a-b-c-d-f，比如从 f回滚到b，**`回滚后, c d f 节点的更改会汇总在非暂存区，git会跟踪的文件区域，使用`git add`可以将文件添加到暂存区，本质上之前的修改都会保存，不会有文件更改丢失。`**
   
2. [回滚]  `git reset --soft <commit>` 
   1. 例子: a-b-c-d-f，比如从 f回滚到b，**`会滚后, c d f 节点的更改会汇总在暂存区，本质上文件的修改都会存在，不会有文件更改丢失`**


3. [回滚]  `git reset --hard <commit>` (危险: 容易丢失数据，但是可以使用 reflog通过操作id 回滚回来) 
   1. 例子: a-b-c-d-f，比如从 f回滚到b，**`会滚后, c d f 节点的更改都不会存在，直接就是b当时最后的提交，这样就会导致 c d f 节点的更改全部丢失，除非你确实不需要c d f节点的更改，这样可以简化操作。否则不要这样使用。 这样危险的操作会导致 丢失历史数据，当然可以通过 reflog 操作号来进行 回滚找回。 `**


总结
=======
Reset很好用，但是在使用`git reset --hard`模式下一定要小心，虽然可以用reflog来找回，但是也是依赖本地的操作号。

