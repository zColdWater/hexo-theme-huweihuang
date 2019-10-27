---
title: "[Jenkins] 添加slave节点并使用节点构建"
catalog: true
toc_nav_num: true
date: 2019-10-22 21:00:30
subtitle: "如何使用Jenkins的Slave节点，控制其他机器进行构建？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Jenkins
catagories:
- Jenkins
---


### 需求背景

Flutter项目提交代码，就触发自动打包上传蒲公英流程。

> 这里我只演示iOS的Shell，Android相比与iOS在打包流程上会简单一些，一般项目配置好，直接build，除非有一些特殊的加固加壳等操作。

希望整个流程会解放大家的双手。

### 步骤一: 配置凭据 

下面是看图说话:   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave1.png" height="150" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave2.png" height="150" />  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave3.png" height="150" />


### 步骤二: 添加slave节点 

下面是看图说话:    

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave4.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave5.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave6.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave7.png" height="150" />   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave8.png" height="150" />   


### 步骤三: 编写iOS打包Job

新建一个自由风格的任务，然后看图说话:  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave9.png" height="150" />     


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave10.png" height="150" />    


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave11.png" height="150" />     


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/slave12.png" height="150" />     


### 总结

自此iOS的自动化打包就做好了，我们Jenkins服务开在我们的Docker容器里，然后奴役一个macOS系统的电脑作为打包iOS的机器，现在我们更新项目提交到git仓库，jenkins就会自动轮训提交，然后为我们打包上传至蒲公英，当然可以有更完善的流程，比如ipa留存档，蒲公英发布系统私有化部署等。  


