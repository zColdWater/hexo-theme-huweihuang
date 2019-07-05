---
title: "[CI] 使用WebHook持续部署GitBook"
catalog: true
toc_nav_num: true
date: 2019-07-05 16:19:30
subtitle: "记录如何使用WebHook进行持续部署"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- CI
catagories:
- CI
---

背景
=======

作为一名有"痣"青年，一直致力于追求最帅最靓的文档，然而天不长眼 公司既然花钱部署了一个叫 `Confluence`的家伙，开始寻思为了生活就算了 虽然之前没用过，但看着也还凑合，但随着后面大量使用的时候，我们才发现，我货对`markdown`的支持实在太差了，部署在我们公司的 `Confluence` 根本不能用 "马克党"，在坚持了快2月左右的情况下，全组成员 基本全军覆膜，文档写起来超级麻烦不说，还不好排版，因为根本用不惯`WiKi`的格式。 大家心理都暗自骂娘。  

最后大家觉得，都是搞技术的，什么事情是可以难到程序猴的，答案肯定是 不可能滴！，我就随口说了句 `gitbook` 怎么样，万万没想到，大家就这么愉快的同意了，当然搭建`gitbook`的责任就落在我身上啦。
经过努力，我们终于搞定了这一切，用起来是真滴舒服。 不光帅 还漂亮，还有`git`管理 有历史，根本不怕文档丢了，或者怎么样。

分析需求
=======

1. 搭建`gitbook`
2. 多人编辑后提交并且 push 代码到远端，自动触发构建部署任务。
3. 其他组小伙伴提MR，也要自动触发构建部署任务。 

解决需求
=======

1. 搭建`gitbook`到本地服务器，我想这是最简单的了。 
   1. npm install gitbook-cli -g
   2. gitbook init
   3. gitbook build  # 执行这个命令后 会生成 _book目录，进入_book目录 然后打开index.html文件，就是我们文档。 

2. 当 git `Push` `MergeRequest` 的时候触发自动构建。 这里就用到了 `Webhook`工具，一句话 `Webhook`工具就是 hook 各种git操作的工具，比如 hook 住`git push`的操作，然后回调给你服务器的API。 举个例子，A同学操作仓库1 ->push 仓库1-> gitlab hook -> 调用你填入的API -> 你的Server接到了调用，知道是仓库有push的操作了，然后执行一些事情。 **备注: webhook的工具，gitlab，github 都有集成，所以如果您在这两个上面维护代码，配置下就可以使用了。** 
   


结论
=======

