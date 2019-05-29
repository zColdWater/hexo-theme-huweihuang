---
title: "[Git] 成员权限"
catalog: true
toc_nav_num: true
date: 2019-05-28 03:59:30
subtitle: "温故Git的成员权限"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Git
catagories:
- Git
---

> 当前环境: git version 2.16.3

权限
=======

GitLab官方链接: https://docs.gitlab.com/ee/user/permissions.html#fn:2

下面我整理下几个常用的权限。

| 权限        | Guest           | Reporter  | Developer | Master | Owner
| ------------- |:-------------:| -----:| :----------:|:--------:|:------:|
| 查看项目代码      | right-aligned | $1600 |   $1600 | $1600 | $1600 |
| 拉取项目代码      | centered      |   $12 |     $12 |     $12 |   $12 |
| 下载项目 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 创建MergeRequest | are neat      |    $1 |      $1 |     $1 |    $1 |
| 创建分支 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 上传代码到无保护分支 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 强制上传代码到无保护分支 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 删除无保护代码分支 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 上传有保护分支代码 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 强制上传代码到有保护分支 | are neat      |    $1 |      $1 |     $1 |    $1 |
| 删除有保护代码分支 | are neat      |    $1 |      $1 |     $1 |    $1 |



