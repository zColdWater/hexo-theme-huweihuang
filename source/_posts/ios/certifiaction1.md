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

# Xcode提示 开发证书不存在本地钥匙串

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification1.png" height="150" />

# 解决方法一（不推荐）

`Revoke`然后创建一个新的，应该先去自己的账号看下如果可以用，下载之前的可以用的，而不是直接`Revoke`，如果之前你用`P12`导出的开发证书给你的同事A B C 安装过了，你`Revoke`了，他们就不能使用了，只能你重新生成`P12`给他们了。

除非真的过期了，或者真的对其他人没有影响的情况再`Revoke`。

# 解决方法二 (推荐)

1. 按 `cmd + ,` 唤起Preferences菜单.  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification4.png" height="350" />

2. 选择`Accounts`点击证书所在的`Apple ID -> 点击Manage Certificates...`
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification2.png" height="250" />

3. 点击+号，选iOS Development，点击Done。
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/certification3.png" height="250" />

4. 完成



