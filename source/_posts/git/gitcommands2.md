---
title: "[Git] fast-forward non-fast-forward 区别"
catalog: true
toc_nav_num: true
date: 2018-11-06 00:06:30
subtitle: "fast-forward non-fast-forward 区别在哪？ 如何使用？"
header-img: "https://upload-images.jianshu.io/upload_images/1793544-318f7bb67d0cb61f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"
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

    * 不使用 `fast-forward(non-fast-forward)`:
        ```
        git merge --no-ff feature
        ```
        

fast-forward
=======
> 

  * `[场景一]` 建议改变别人的项目 

    使用forks提出更改的一个很好的例子是bug修复。您可以：不是为您找到的错误记录问题，而是：

    1.Fork存储库。  
    2.做出修复。  
    3.向项目所有者提交拉取请求。(GitHub使用PullRequest/GitLab使用MergeRequest)

non-fast-forward
=======
> Fork会创建一个仓库的副本。副本存储库允许您自由地尝试更改，而不会影响原始项目。
> 最常见的是，Fork用于建议对其他人项目的更改或使用其他人的项目作为您自己想法的起点。

  * `[场景一]` 建议改变别人的项目 

    使用forks提出更改的一个很好的例子是bug修复。您可以：不是为您找到的错误记录问题，而是：

    1.Fork存储库。  
    2.做出修复。  
    3.向项目所有者提交拉取请求。(GitHub使用PullRequest/GitLab使用MergeRequest)

Reference：  
github help：https://help.github.com/articles/fork-a-repo/   
syncing fork： https://help.github.com/articles/syncing-a-fork/  
pullrequest： https://help.github.com/articles/about-pull-requests/  
---