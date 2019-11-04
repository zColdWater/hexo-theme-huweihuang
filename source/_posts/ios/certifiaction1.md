---
title: "[iOS] Your account already has a signing certificate for this machine but it is not pre..."
catalog: true
toc_nav_num: true
date: 2019-11-04 10:00:30
subtitle: "解决证书配置问题"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- iOS
catagories:
- iOS
---

# Xcode提示

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification1.png" height="350" />

# 解决方法一

`直接按照Xcode的提示`Revoke`掉，然后重新新建证书。`

# 解决方法二

1. 按 `cmd + ,` 唤起Preferences菜单.  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification4.png" height="350" />

2. 选择`Accounts`点击证书所在的`Apple ID -> 点击Manage Certificates...`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification2.png" height="350" />

3. 点击+号，选iOS Development，点击Done。
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification3.png" height="350" />

4. 完成



