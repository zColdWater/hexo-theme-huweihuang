---
title: "[Basic] 第三方登录原理"
catalog: true
toc_nav_num: true
date: 2019-06-15 08:34:30
subtitle: "第三方登录原理讲解"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---

> 本篇文章截取自阮一峰博客片段

GitHub OAuth 第三方登录
=======

第三方登录的原理:
    所谓第三方登录，实质就是 OAuth 授权。用户想要登录 A 网站，A 网站让用户提供第三方网站的数据，证明自己的身份。获取第三方网站的身份数据，就需要 OAuth 授权。举例来说，A 网站允许 GitHub 登录，背后就是下面的流程。

        A 网站让用户跳转到 GitHub。
        GitHub 要求用户登录，然后询问"A 网站要求获得 xx 权限，你是否同意？"
        用户同意，GitHub 就会重定向回 A 网站，同时发回一个授权码。
        A 网站使用授权码，向 GitHub 请求令牌。
        GitHub 返回令牌.
        A 网站使用令牌，向 GitHub 请求用户数据。

参考:   
http://www.ruanyifeng.com/blog/2019/04/github-oauth.html