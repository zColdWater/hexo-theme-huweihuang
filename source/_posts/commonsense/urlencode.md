---
title: "[Basic] URLEncode"
catalog: true
toc_nav_num: true
date: 2018-11-08 10:32:30
subtitle: "什么是URLEncode？ 我们为什么需要它？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/safecode-min.png"
tags:
- Basic
catagories:
- Basic
---

URLEncode 由来
=======
在网络传输中，我们会通过一个URL来访问服务端主机上面资源，这个URL是根据RFC的规则拟定的。 [RFC3986](http://www.ietf.org/rfc/rfc3986.txt) 中指定了以下字符为保留字符：  

    ! * ' ( ) ; : @ & = + $ , / ? # [ ]  

---
不安全字符：还有一些字符，当他们直接放在Url中的时候，可能会引起解析程序的歧义。这些字符被视为不安全字符，下面我们做一些例子。

> 注意: URLEncode是如何给一个字符串进行编码的呢？ 比如我们使用的 `UTF-8 百分比编码的方式`
对 `abc&+你好日本文と`进行编码，那么流程是什么呢? 

> 首先对 `abc&+你好日本文と` 进行[UTF-8编码](https://zcoldwater.github.io/blog/article/commonsense/unicode/)得到 `\x61\x62\x63\x26\x2b\xe4\xbd\xa0\xe5\xa5\xbd\x08\xe6\x97\xa5\xe6\x9c\xac\xe6\x96\x87\xe3\x81\xa8`

例如我在Google下面搜索 "asd"

    // asd 还是 asd 未被URLEncode百分比utf-8编码
    https://www.google.com/search?safe=active&source=hp&ei=cuDjW8BAi4bzBZ-7vdgF&q=asd&oq=asd&gs_l=psy-ab.3..0i67k1l3j0l7.2033.2553.0.2937.5.4.0.0.0.0.159.310.0j2.3.0....0...1c.1.64.psy-ab..2.3.502.6..35i39k1.192.F4Fr2CiPDFs

例如我在Google下面搜索 "asd&"

    // asd& 被URLEncode百分比utf-8编码为 %26
    https://www.google.com/search?safe=active&ei=deDjW_vqJon58QWy-5yQDw&q=asd%26&oq=asd%26&gs_l=psy-ab.3..0i13k1l10.290493.290493.0.290821.1.1.0.0.0.0.322.322.3-1.1.0....0...1c.1.64.psy-ab..0.1.321....0.8Rz3aqV7Q48

例如我在Google下面搜索 "asd你好"

    // asd你好 被URLEncode百分比utf-8编码为 asd%E4%BD%A0%E5%A5%BD
    https://www.google.com/search?safe=active&ei=meHjW8amKcen8QWl0I8g&q=asd%E4%BD%A0%E5%A5%BD&oq=asd%E4%BD%A0%E5%A5%BD&gs_l=psy-ab.3...302565.303529.0.304354.7.7.0.0.0.0.291.686.0j1j2.3.0....0...1c.1j4.64.psy-ab..4.2.433...0j35i39k1j0i10k1.0.DrodH9-dtyc

例如我在Google下面搜索 "asd??"

    // asd?? 被URLEncode百分比utf-8编码为 asd%3F%3F
    https://www.google.com/search?safe=active&ei=y-LjW-6LGYGH8wXtjZm4CQ&q=asd%3F%3F&oq=asd%3F%3F&gs_l=psy-ab.3..35i39k1j0l6j0i10k1l3.246838.246838.0.247403.1.1.0.0.0.0.219.219.2-1.1.0....0...1c.2.64.psy-ab..0.1.219....0.tkGiKjp3OVw

RFC3986协议：http://www.ietf.org/rfc/rfc3986.txt

---