---
title: "Fork"
catalog: true
toc_nav_num: true
date: 2018-11-06 00:06:30
subtitle: "Fork的用处是什么？"
header-img: "https://upload-images.jianshu.io/upload_images/1793544-35a59cc66546fe40.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"
tags:
- Git
catagories:
- Git
---

什么是Fork?
=======
> Fork会创建一个仓库的副本。副本存储库允许您自由地尝试更改，而不会影响原始项目。
> 最常见的是，Fork用于建议对其他人项目的更改或使用其他人的项目作为您自己想法的起点。


  * `[场景一]` 建议改变别人的项目 

    使用forks提出更改的一个很好的例子是bug修复。您可以：不是为您找到的错误记录问题，而是：

    1.Fork存储库。  
    2.做出修复。  
    3.向项目所有者提交拉取请求。(GitHub使用PullRequest/GitLab使用MergeRequest)

  * `[场景二]` 使用别人的项目作为您自己想法的起点。 

    开源的理念是通过共享代码，我们可以做出更好，更可靠的软件。
    当你觉得我可以对其他人的库有自己的理解，我们做一些自定义的Modify来满足自己的需求，这个产品的轨迹完全导向你的时候，我们也可以用到Fork。

Syncing a fork
=======
> 同步fork简单的来讲，比如你fork的项目已经距离你fork之后有很多改动，你需要把他的改动同步到你的项目里。

![官方文档](https://help.github.com/articles/syncing-a-fork/ "同步Fork文档")描述的已经很清楚了，可以直接参考。
    


About pull requests
=======
> `PullRequet`是在使用Github来管理代码用到的一个工具，和GitLab的`MergeRequest`类似，它是用来把你的项目的改动，提交给目标项目的操作。 比如: 你Fork了一个项目在你自己的名下，然后你发现它的一个bug，并且修复了你想提交给原仓库，这个时候就需要`PullRequest`来帮助你完成了。

![官方文档](https://help.github.com/articles/about-pull-requests/ "PullRequest")描述的已经很清楚了，可以直接参考。


Reference：  
github help：https://help.github.com/articles/fork-a-repo/   
syncing fork： https://help.github.com/articles/syncing-a-fork/  
pullrequest： https://help.github.com/articles/about-pull-requests/  
---