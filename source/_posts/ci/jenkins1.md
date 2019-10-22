---
title: "[Jenkins] 自动部署应用"
catalog: true
toc_nav_num: true
date: 2019-10-22 17:00:30
subtitle: "如何通过Jenkins自动部署应用？"
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

步骤一: 准备好可以打包的代码在git仓库(本篇不涉及)
步骤二: 在一个服务器上部署好jenkins(本篇不涉及)
步骤三: jenkins安装 publish over ssh 插件
步骤四: publish over ssh 连接选择 1.密码登录 2.sshkey登录 
步骤五: 配置Job
步骤六: 构建测试

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish1.png" height="350" />

### 具体步骤

步骤一 源代码git仓库这个开发应该都会创建，直接跳过这步。      

步骤二 搭建Jenkins服务器，这个也不难，首先你得有一个Linux服务器，然后去jenkins官方看文档照着安装就可以了，跳过。  

步骤三/四/五 jenkins安装 publish over ssh 插件，配置服务器和Job，这个我放图，大家自己看图，语言有时候是苍白的，你看着也烦，我也知道，哈哈。  

> 这里需要注意的是，由于我们的目标服务器在跳板机上，只支持 ssh key 的登录，不能用密码登录，所以这里的密码登录演示是我用自己的腾讯云服务器做的演示。

安装 publish over ssh 插件

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish2.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish3.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish4.png" height="350" />

配置 publish over ssh 你的ssh登录的目标服务器

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish5.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish6.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish7.png" height="350" />

配置你job的publish over ssh插件的选项

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish8.png" height="350" />


步骤六 构建测试，语言还是苍白的，直接上图最好了。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish9.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish10.png" height="350" />

可以看出 我们成功的将 build 后的jar包 同步到 目标服务器的指定目录下，通过publish over ssh插件。


### 构建脚本



### 同步到目标服务器之后的脚本

