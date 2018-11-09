---
title: "[Basic] 关于 Content-Type"
catalog: true
toc_nav_num: true
date: 2018-11-09 13:55:30
subtitle: "认真的看一下 Content-Type 是什么？ 如何使用。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/achievement-min.png"
tags:
- Basic
catagories:
- Basic
---

Content-Type 介绍
=======
互联网媒体类型（`Internet media type`，也称为MIME类型（`MIME type`）或内容类型（`content type`））是给互联网上传输的内容赋予的分类类型。一份内容的互联网媒体类型是由其文件格式与内容决定的。互联网媒体类型与文件拓展名相对应，因此计算机系统常常通过拓展名来确定一个文件的媒体类型并决定与其相关联的软件。互联网媒体类型的分类标准由互联网号码分配局（IANA）发布。1996年十一月，媒体类型在`RFC 2045`中被最初定义，当时仅被使用在`SMTP`协议的电子邮件中。现在其他的协议（比如`HTTP`或者`SIP`）也都常使用`MIME`类型。 一个`MIME`类型至少包括两个部分：一个类型（`type`）和一个子类型（`subtype`）。此外，它还可能包括一个或多个可选参数（`optional parameter`）。比如，HTML文件的互联网媒体类型可能是  

    // text: 主类型 
    // html: 子类型 
    // charset = UTF-8 可选参数
    text/html; charset = UTF-8
[点击此处 ➡️ 查看所有的媒体类型](https://www.iana.org/assignments/media-types/media-types.xhtml#text)

具体每种媒体类型是什么意思 这个就大家自行 [Google](https://www.google.com/)。

注意
=======
> 这里说下几点，以前在我们刚接触开发的时候一些知识点模糊不清的地方进行解答。

[问答 来自StackOverflow:](https://stackoverflow.com/questions/5809099/does-the-http-protocol-support-multiple-content-types-in-response-headers)  
Q: 请问我们的 Content-Type 可以设置多个类型么？ 例如: `Content-Type: application/json text/json `  
A: 不合法，只能 单一类型/子类型，后跟可选参数。

[问答 来自StackOverflow:](https://stackoverflow.com/questions/5661596/do-i-need-a-content-type-for-http-get-requests)  
Q: 请问我使用GET请求还需要设置`Content-Type`么?  
A: 不需要, 根据[RFC 7231第3.1.5.5节：](https://tools.ietf.org/html/rfc7231#section-3.1.1.5)
Content-Type应仅为for PUT和POSTrequests 设置HTTP标头。并且GET会使用URLPercentEncoding By UTF-8 的传输类型。

[问答 来自StackOverflow:](https://stackoverflow.com/questions/12539058/is-there-a-default-mime-type)  
Q: 请问默认的`Content-Type`是什么？  
A: application/octet-stream 这个类型表示`它说“这里有一堆字节，希望你的终端有一个应用程序知道如何处理它们”`  




Reference：    
Media type： https://en.wikipedia.org/wiki/Media_type  
iana： https://www.iana.org/assignments/media-types/media-types.xhtml#text