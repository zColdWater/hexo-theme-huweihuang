---
title: "[Jenkins] 自动部署应用"
catalog: true
toc_nav_num: true
date: 2019-10-22 17:00:30
subtitle: "如何自动部署应用通过Jenkins？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Jenkins
catagories:
- Jenkins
---
 
这里做个补充:   
我之前一直以为跳板机是我无法跳过的，我连接我的服务器一定要先过跳板机，后台devops同事跟我说，第一次走跳板机连上服务器，配置sshkey和本地的机器，
下次就可以直接从本地连我的服务器了，跳过跳板机。   

为什么做这个补充，因为后面我们部署的服务器是公司服务器，躲在跳板机后面，所以我们这里才做这个补充。

### 整体流程 

我在Jenkins上面点击构建，自动帮我用maven打包并且发布在我要发布的服务器上，我画了个图来解释我要做什么。   

如果要实现我可以分如下几个步骤:    

1. 准备好可以打包的代码在git仓库(本篇不涉及)
2. 在一个服务器上部署好jenkins(本篇不涉及)
3. jenkins安装 publish over ssh 插件
4. publish over ssh 连接选择 1.密码登录 2.sshkey登录 

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish1.png" height="350" />


### 具体步骤



