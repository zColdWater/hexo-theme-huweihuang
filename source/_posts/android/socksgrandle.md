---
title: "[Android] 设置Socks代理下载Gradle"
catalog: true
toc_nav_num: true
date: 2019-06-07 22:00:30
subtitle: "如何设置Socks代理下载Gradle"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- Android
catagories:
- Android
---

> 在下载Gradle的时候，我发现AndroidStudio根本下载不下来，导致浪费大量时间，下面是有Shadowsocks的同学环境下，我们如何运用Shadowsocks让AS去帮我们成功下载到Gradle呢。 当然这个问题也只有大陆程序员会遇到，毕竟天朝的墙还是很高的。

步骤一: **Mac下，~/.gradle文件夹下打开/新建grade.properties文件,设置代理.** 设置内容为: `org.gradle.jvmargs=-DsocksProxyHost=127.0.0.1 -DsocksProxyPort=1086 -DsocksNonProxyHosts=*.xcompany.com`

步骤二: 打开`Mac`上的`Shadowsocks`,找到`偏好设置`选项 点击进入。点击`高级`Tab 如下图配置，然后开启 Shadowsocks 就可以让AndroidStudio挂上代理了 下载一些 高墙之外的代码和模块。
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/shadowsocks_advance.png" height="200" />


