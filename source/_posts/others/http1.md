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

> 客户端可以采用两种方式来保存这个 Cookie 对象，一种方式是保存在客户端内存中，称为临时 Cookie，浏览器关闭后这个 Cookie 对象将消失。 

> 另外一种方式是保存在客户机的磁盘上，称为永久 **Cookie**。以后客户端只要访问该网站，就会将这个 **Cookie** 再次发送到服务器上，前提是这个 **Cookie** 在有效期内，这样就实现了对客户的跟踪。

> **`Cookie`** 是可以被禁止的，当你打开 **`Chrome`**，在设置里面关闭 **`Cookie`**，那么你将再也无法登录淘宝页面。


**`Session：`**在服务器端会创建一个 session 对象，产生一个 sessionID 来标识这个 session 对象，然后将这个 sessionID 放入到 Cookie 中发送到客户端，下一次访问时，sessionID 会发送到服务器，在服务器端进行识别不同的用户。  

> 每一个用户都有一个不同的 session，各个用户之间是不能共享的，是每个用户所独享的，在 session 中可以存放信息。

> Session的实现依赖于Cookie，如果Cookie被禁用，那么session也将失效。


在 HTTP 1.1 版本中，默认情况下所有连接都被保持，如果加入 "Connection: close" 才关闭。目前大部分浏览器都使用 HTTP 1.1 协议，也就是说默认都会发起 Keep-Alive 的连接请求了，所以是否能完成一个完整的 Keep-Alive 连接就看服务器设置情况。

**HTTP Keep-Alive 简单说就是保持当前的TCP连接，避免了重新建立连接。HTTP 是一个无状态无连接的协议，那么这是不是与 Keep-Alive 冲突？**

1. Keep-Alive 与无连接的特性冲突，而对于无状态的特性两者并无矛盾，HTTP 无状态无连接是在 1.0 版本中就规定的，而 Keep-Alive 则是在 1.1 版本中才被添加入规范。

2. 无连接的意思是限制每个连接只有一个请求的意思，在服务器处理完客户的请求，并收到客户的反应，即断开。通过这种方式可以节省传输时间。

3. Keep-Alive 确实破坏了这一特性，而无状态协议则意味着每个请求都是独立的，互不干扰的，互相没有记忆的。所以才需要有会话跟踪这种机制来识别用户。




HTTP Keep-Alive & TCP Keep-Alive
=======

1. **HTTP Keep-Alive:**

    在http早期，每个http请求都要求打开一个tpc socket连接，并且使用一次之后就断开这个tcp连接。

    使用keep-alive可以改善这种状态，即在一次TCP连接中可以持续发送多份数据而不会断开连接。通过使用keep-alive机制，可以减少tcp连接建立次数，也意味着可以减少TIME_WAIT状态连接，以此提高性能和提高httpd服务器的吞吐率(更少的tcp连接意味着更少的系统内核调用,socket的accept()和close()调用)。

    但是，keep-alive并不是免费的午餐,长时间的tcp连接容易导致系统资源无效占用。配置不当的keep-alive，有时比重复利用连接带来的损失还更大。所以，正确地设置keep-alive timeout时间非常重要。

    **当httpd守护进程发送完一个响应后，理应马上主动关闭相应的tcp连接，设置 `keepalive_timeout`后，httpd守护进程会想说：”`再等等吧，看看浏览器还有没有请求过来`”，这一等，便是`keepalive_timeout`时间。如果守护进程在这个等待的时间里，一直没有收到浏览发过来http请求，则关闭这个http连接。**



2. **TCP Keep-Alive:**

    http keep-alive与tcp keep-alive，不是同一回事，意图不一样。http keep-alive是为了让tcp活得更久一点，以便在同一个连接上传送多个http，提高socket的效率。而tcp keep-alive是TCP的一种检测TCP连接状况的保鲜机制。

    keepalive是TCP保鲜定时器，当网络两端建立了TCP连接之后，闲置idle（双方没有任何数据流发送往来）了tcp_keepalive_time后，服务器内核就会尝试向客户端发送侦测包，来判断TCP连接状况(有可能客户端崩溃、强制关闭了应用、主机不可达等等)。如果没有收到对方的回答(ack包)，则会在 tcp_keepalive_intvl后再次尝试发送侦测包，直到收到对对方的ack,如果一直没有收到对方的ack,一共会尝试 tcp_keepalive_probes次，每次的间隔时间在这里分别是15s, 30s, 45s, 60s, 75s。如果尝试tcp_keepalive_probes,依然没有收到对方的ack包，则会丢弃该TCP连接。TCP连接默认闲置时间是2小时，一般设置为30分钟足够了。
    
    也就是说，仅当nginx的keepalive_timeout值设置高于tcp_keepalive_time，并且距此tcp连接传输的最后一个http响应，经过了tcp_keepalive_time时间之后，操作系统才会发送侦测包来决定是否要丢弃这个TCP连接。一般不会出现这种情况，除非你需要这样做。



参考:  

http://www.nowamagic.net/academy/detail/23350305  

