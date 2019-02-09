---
title: "[Basic] 什么是CORS？为什么浏览器要有跨域这种说法？"
catalog: true
toc_nav_num: true
date: 2019-01-09 10:09:30
subtitle: "什么是跨域？为什么要有域这种限制？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/code-min.png"
tags:
- Basic
catagories:
- Basic
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

什么是跨域?
=======
> Cross-origin resource sharing (CORS) 全称。 

> Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served. A web page may freely embed cross-origin images, stylesheets, scripts, iframes, and videos. Certain "cross-domain" requests, notably Ajax requests, are forbidden by default by the same-origin security policy.

上面信息来源自WiKi，中文意思就是，我们的Web可以自由的使用一些标签即使标签里的资源是跨域的，但是Ajax请求是被强调必须禁止的。

那么什么算做跨域呢?  
> 造成跨域的两种策略
浏览器的同源策略会导致跨域，这里同源策略又分为以下两种

1. DOM同源策略：禁止对不同源页面DOM进行操作。这里主要场景是iframe跨域的情况，不同域名的iframe是限制互相访问的。

2. XmlHttpRequest同源策略：禁止使用XHR对象向不同源的服务器地址发起HTTP请求。

**只要协议、域名、端口有任何一个不同，都被当作是不同的域，之间的请求就是跨域操作。**

为什么有域的限制?
=======
了解完跨域之后，想必大家都会有这么一个思考，为什么要有跨域的限制，浏览器这么做是出于何种原因呢。其实仔细想一想就会明白，跨域限制主要是为了安全考虑。

AJAX同源策略主要用来防止CSRF跨站请求伪造（英语：Cross-site request forgery）攻击。如果没有AJAX同源策略，相当危险，我们发起的每一次HTTP请求都会带上请求地址对应的cookie，那么可以做如下攻击：

1. 用户登录了自己的银行页面 http://mybank.com，http://mybank.com向用户的cookie中添加用户标识。

2. 用户浏览了恶意页面 http://evil.com。执行了页面中的恶意AJAX请求代码。

3. http://evil.com向http://mybank.com发起AJAX HTTP请求，请求会默认把http://mybank.com对应cookie也同时发送过去。

4. 银行页面从发送的cookie中提取用户标识，验证用户无误，response中返回请求数据。此时数据就泄露了。

5. 而且由于Ajax在后台执行，用户无法感知这一过程。

DOM同源策略也一样，如果iframe之间可以跨域访问，可以这样攻击：

1. 做一个假网站，里面用iframe嵌套一个银行网站 http://mybank.com。

2. 把iframe宽高啥的调整到页面全部，这样用户进来除了域名，别的部分和银行的网站没有任何差别。

3. 这时如果用户输入账号密码，我们的主网站可以跨域访问到http://mybank.com的dom节点，就可以拿到用户的输入了，那么就完成了一次攻击。所以说有了跨域跨域限制之后，我们才能更安全的上网了。

