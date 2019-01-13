---
title: "[Basic] HTTP协议"
catalog: true
toc_nav_num: true
date: 2019-01-13 10:37:30
subtitle: "什么是HTTP协议?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

定义
=======

> HTTP，全称为 HyperText Transfer Protocol，即为超文本传输协议。是互联网应用最为广泛的一种网络协议，所有的 www 文件都必须遵守这个标准。

**HTTP 特性：**

HTTP 是无连接无状态的
HTTP 一般构建于 TCP/IP 协议之上，默认端口号是 80
HTTP 可以分为两个部分，即请求和响应。

**HTTP 请求：**

HTTP 定义了在与服务器交互的不同方式，最常用的方法有 4 种，分别是 GET，POST，PUT， DELETE。URL 全称为资源描述符，可以这么认为：一个 URL 地址，对应着一个网络上的资源，而 HTTP 中的 GET，POST，PUT，DELETE 就对应着对这个资源的查询，修改，增添，删除4个操作。

**GET 请求和 POST 请求的区别:**

1. GET 和 POST 请求参数位置不同，从上面两个请求报文可以看出，GET 请求对应的参数放在 URL 中，而 POST 请求对应的参数放在 HTTP 请求主体中。(但是这只是一种约定，GET 请求中出现 Body 也是被允许的)  

2. 虽然 HTTP 协议的 RFC规范 并没有详细规定 URL 的最大字符长度限制，但实际上，在浏览器或者服务器中总会存在限制的，这就导致了 GET 请求中参数数量是有限的。  
3. 处于安全考虑，在一些涉及安全的请求比如：登录请求需要用 POST 提交表单，而GET 请求一般用来获取静态资源。  

4. GET 请求可以被缓存，可以被收藏为书签，但 POST 可以被缓存，但不能被收藏为书签。  

5. GET 请求的参数在 URL 中，因此绝不能用 GET 请求传输敏感数据。POST 请求数据则写在 HTTP 的请求头中，安全性略高于 GET 请求。   


**比如常见的状态码：**

1. 200 OK 客户端请求成功。  

2. 301 Moved Permanently 请求永久重定向。  

3. 302 Moved Temporarily 请求临时重定向。  

4. 304 Not Modified 文件未修改，可以直接使用缓存的文件。  

5. 400 Bad Request 由于客户端请求有语法错误，不能被服务器所理解。  

6. 401 Unauthorized 请求未经授权，无法访问。  

7. 403 Forbidden 服务器收到请求，但是拒绝提供服务。服务器通常会在响应正文中给出不提供服务的原因。  

8. 404 Not Found 请求的资源不存在，比如输入了错误的URL。  

9. 500 Internal Server Error 服务器发生不可预期的错误，导致无法完成客户端的请求。  

10. 503 Service Unavailable 服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常。  


**`Cookie：`**Cookie 是 Web 服务器发送给客户端的一小段信息，客户端请求时可以读取该信息发送给服务器端，进而进行用户的识别，对于客户端的每次请求，服务器都会将 Cookie 发送到客户端，客户端保存下来，以便下次使用。  
客户端可以采用两种方式来保存这个 Cookie 对象，一种方式是保存在客户端内存中，称为临时 Cookie，浏览器关闭后这个 Cookie 对象将消失。 

另外一种方式是保存在客户机的磁盘上，称为永久 **Cookie**。以后客户端只要访问该网站，就会将这个 **Cookie** 再次发送到服务器上，前提是这个 **Cookie** 在有效期内，这样就实现了对客户的跟踪。

**`Cookie`** 是可以被禁止的，当你打开 **`Chrome`**，在设置里面关闭 **`Cookie`**，那么你将再也无法登录淘宝页面。


