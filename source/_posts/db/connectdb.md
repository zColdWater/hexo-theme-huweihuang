---
title: "[DB] 使用Navicat同步表结构"
catalog: true
toc_nav_num: true
date: 2019-06-11 20:18:30
subtitle: "Mysql使用Navicat同步表结构"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/nature.jpg"
tags:
- DB
catagories:
- DB
---

> 当前版本: mysql  Ver 14.14 Distrib 5.7.28, for Linux (x86_64) using  EditLine wrapper

自掏腰包从 https://www.mac69.com/mac/53.html 上面购买的破解版本。

做好事不留名，安装前一定要看安装包里面新手必看文档哈，要么不开。 [Navicat中文破解版 MacOS](https://github.com/zColdWater/navicat-premium-mac)

当我们开发实际项目的时候会有多套环境，比如简单的分为 `测试环境` `生产环境` ，那么流程上肯定是测试环境先动，比如添加表，修改字段，当要发布生产环境的时候，就需要把测试环境的库同步到生产环境的库，如果是人工一个一个字段去比对，明显不太现实，因为如果某期大的迭代，表中改动实在太大，所以人工是不靠谱的，这也就是我下面要给大家介绍的办法，幸运的是，我发现`Navicat`这款软件自带了这个方法。

下面就看看我们如何使用 `Navicat` 来同步表结构把。  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat1.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat2.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat3.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat4.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat5.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat6.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat7.png" height="200" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/navicat8.png" height="200" />

同步结构确实简单了很多，并且还有其他同步数据等操作，真的感觉挺强大的，给我这种小白许多关怀，开心儿！ 😄  


