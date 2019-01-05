---
title: "[WKWebView] 如何实现秒开WebView"
catalog: true
toc_nav_num: true
date: 2019-01-06 00:29:30
subtitle: "如何秒开一个WebView呢？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/people-diff-min.png"
tags:
- iOS
catagories:
- iOS
---

解决办法
=======

> 当app使用混合模式开发的时候，H5在业务中的体验最为重要，尤其是一些特殊的场景需要页面秒开的速度，该如何做呢？

1. 离线包，从服务器下载压缩包到本地，webview读取静态资源。

2. 单例WebView，因为WebView每次初始化都巨慢无比，所以给人感觉他很慢，如果公用一个WebView速度会明显提升。

结论
=======
满足上面两条，WebView完全可以达到秒开，对一些重要的页面，离线包只要缓存的静态资源够多，打开速度肯定在半秒以内，但是不建议下发很多资源文件，因为会使App占用大小变大很多，但是很多公共JS/CSS文件可以下发本地，节省时间。 这个结论亲测有效。
