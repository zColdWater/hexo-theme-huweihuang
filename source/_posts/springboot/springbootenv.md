---
title: "[Spring] SpringBoot项目配置环境"
catalog: true
toc_nav_num: true
date: 2019-10-31 17:16:30
subtitle: "如何给SpringBoot项目配置环境？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Spring
catagories:
- Spring
---

正常一个项目，我们项目部署的时候，需要测试环境连接测试环境的数据库，生产连接生产的数据库，所以我们必须有一种方式给予区分，下面就是如何区分环境。  

## 创建application.properties文件

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/springenv1.png" height="200" />


## 分别在环境中添加连接的数据库配置

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/springenv2.png" height="200" />

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/springenv3.png" height="200" />

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/springenv4.png" height="200" />



## 打包按环境进行打包

 <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/springenv5.png" height="200" />


按照上面的流程我最后验证了一下，成功区分了环境，部署在不同机器的两个服务，连接了不同的数据库。


