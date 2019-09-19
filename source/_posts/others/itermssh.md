---
title: "[Basic] iTerm2 ssh连接服务器总断开"
catalog: true
toc_nav_num: true
date: 2019-09-21 12:50:30
subtitle: "解决 iterm2 ssh 连接服务器总断开"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---


# 如何在iTerm2中ssh保持连接不断开

1. 在客户端输入 `vim ~/.ssh/config` 编辑config文件
2. 添加 Host * 和属性 ServerAliveInterval 60， * 代表连接所有的ssh都适用，60代表 60s给Server发一次心跳包保持不断。
```
Host *
ServerAliveInterval 60
```
经过测试 问题完美解决，不论是挂在后台还是长时间不激活iTerm都保持正常连接。


参考: http://bluebiu.com/blog/iterm2-ssh-session-idle.html 
