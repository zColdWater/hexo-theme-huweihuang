---
title: "[Git] 撤销"
catalog: true
toc_nav_num: true
date: 2019-05-28 03:16:30
subtitle: "使用Git时候如何撤销？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

这里要明确一下概念:  当一个文件 未被gitignore无视的文件，都会加入到git的track里来，git add 将文件加入到暂存区。

1. [撤销]  从暂存区 --> 非暂存区  
2. [撤销]  从非暂存区 --> 取消修改  


从暂存区 --> 非暂存区
=======
例如: 你本地有2文件你都修改过，然后你执行了`git add .`将所有更改都提交到了暂存区。 那么你现在希望有一个文件我不想提到暂存区那么该怎么办？  

`git reset HEAD myfile` // 将myfile文件从暂存区移除  

再执行 `git status` 查看结果



从非暂存区 --> 取消修改
=======
`git checkout -- myfile` // 将myfile从非暂存区 取消修改

再执行 `git status` 查看结果



总结
=======
这里介绍了 `从暂存区 --> 非暂存区` 和 `从非暂存区 --> 取消修改` 这两种情况，也是开发中最常见的。

