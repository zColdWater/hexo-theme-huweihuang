---
title: "Git Commands"
catalog: true
toc_nav_num: true
date: 2018-11-05 01:00:30
subtitle: "常用的git命令"
header-img: "https://upload-images.jianshu.io/upload_images/1793544-8272181ec2013baa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"
tags:
- Git
catagories:
- Git
---

Git-三种状态
=======

> 已暂存（staged），已提交（committed）和 已修改（modified）

  * 已暂存（staged） 通俗地说，git会把被git管理的文件的快照放入暂存区域，比如: `git add .` 这步骤其实就是将所有文件添加到暂存区，经过这步骤所有的改动就添加到暂存区了。
  * 已提交（committed）这个状态是通过 提交更新，找到暂存区域的文件，将快照永久性存储到 git 仓库目录 达到的。 例如 `git commit` 这其实就是将暂存区的文件状态 改变成已提交，接下来就可以使用`git push`来上传代码到远端了。
  * 已修改（modified）任何添加git管理的文件发生了变化都会改变文件状态，比如: 修改一个文件内容，那么文件状态如果之前不是`modified`状态 就会变成这个状态。

---