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

URLEncode 介绍
=======

百分号编码（英语：Percent-encoding）, 也称作URL编码（英语：URL encoding）, 是特定上下文的统一资源定位符 (URL)的编码机制. 实际上也适用于统一资源标志符（URI）的编码。也用于为`"application/x-www-form-urlencoded"` MIME准备数据, 因为它用于通过HTTP的请求操作(request)提交HTML表单数据。

URI所允许的字符分作保留与未保留。保留字符是那些具有特殊含义的字符. 例如, 斜线字符用于URL (或者更一般的, URI)不同部分的分界符. 未保留字符没有这些特殊含义. 百分号编码把保留字符表示为特殊字符序列。上述情形随URI与URI的不同版本规格会有轻微的变化。  

    RFC 3986 section 2.2 保留字符 (2005年1月)
    !	*	'	(	)	;	:	@	&	=	+	$	,	/	?	#	[	]
    RFC 3986 section 2.3 未保留字符 (2005年1月)
    A	B	C	D	E	F	G	H	I	J	K	L	M	N	O	P	Q	R	S	T	U	V	W	X	Y	Z
    a	b	c	d	e	f	g	h	i	j	k	l	m	n	o	p	q	r	s	t	u	v	w	x	y	z
    0	1	2	3	4	5	6	7	8	9	-	_	.	~	
    URI中的其它字符必须用百分号编码。

---

URLEncode 背景
=======

[`###这里引述了阮一峰老师的文章###`](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)  

URL只能使用英文字母、阿拉伯数字和某些标点符号，不能使用其他文字和符号。比如，世界上有英文字母的网址"http://www.abc.com"，但是没有希腊字母的网址"http://www.aβγ.com"（读作阿尔法-贝塔-伽玛.com）。这是因为网络标准RFC 1738做了硬性规定：
```
"...Only alphanumerics [0-9a-zA-Z], the special characters "$-_.+!*'()," [not including the quotes - ed], and reserved characters used for their reserved purposes may be used unencoded within a URL."
"只有字母和数字[0-9a-zA-Z]、一些特殊符号"$-_.+!*'(),"[不包括双引号]、以及某些保留字，才可以不经过编码直接用于URL。"
```
这意味着，如果URL中有汉字，就必须编码后使用。

URLEncode 规则
=======

> 注意: URLEncode是如何给一个字符串进行编码的呢？ 比如我们使用的 `UTF-8 百分比编码的方式`
对 `abc&+你好日本文と`进行编码，那么流程是什么呢? 

1. 首先对 `abc&+你好日本文と` 进行[UTF-8编码](https://zcoldwater.github.io/blog/article/commonsense/unicode/)得到([转码工具](https://www.browserling.com/tools/utf8-encode))  `\x61\x62\x63\x26\x2b\xe4\xbd\xa0\xe5\xa5\xbd\x08\xe6\x97\xa5\xe6\x9c\xac\xe6\x96\x87\xe3\x81\xa8`

2. 其次，识别出不需要编码的字符，比如未保留字符。 程序识别到`\x61\x62\x63`是`abc`，因为`abc`是字母 是未保留字符，根据URLEncode RFC规则 是不需要编码的。

3. 然后对URL中保留字符 和 非未保留字符(例如 中文，日文等等)，进行百分比编码，哪些需要编码，哪些不要编码，是根据RFC协议拟定的。  

    * 对保留字符进行百分比Encode
    `abc\x26\x2b\xe4\xbd\xa0\xe5\xa5\xbd\x08\xe6\x97\xa5\xe6\x9c\xac\xe6\x96\x87\xe3\x81\xa8`接着程序识别到`\x26\x2b`是`&+`是保留字符需要编码，根据Unicode得到百分比Encode为`%26%2b`，现在我们的字符串变成这样`abc%26%2b\xe4\xbd\xa0\xe5\xa5\xbd\x08\xe6\x97\xa5\xe6\x9c\xac\xe6\x96\x87\xe3\x81\xa8`

    * 对非未保留字符(例如 中文，日文等等) 进行百分比Encode
    你好日本文と的utf8编码是`\xe4\xbd\xa0\xe5\xa5\xbd\x08\xe6\x97\xa5\xe6\x9c\xac\xe6\x96\x87\xe3\x81\xa8`根据URLEncode RFC规则 非字符特殊字符集是需要编码的，将`\x`替换成`%`进行百分比编码后`%e4%bd%a0%e5%a5%bd%08%e6%97%a5%e6%9c%ac%e6%96%87%e3%81%a8`

4. 最终结果:`abc%26%2b%e4%bd%a0%e5%a5%bd%08%e6%97%a5%e6%9c%ac%e6%96%87%e3%81%a8`

5. 验证

    例如我在Google下面搜索 `abc&+你好日本文と` 得到如下URL  

    https://www.google.com/search?safe=active&source=hp&ei=QvvjW8T7CIrQ8wXXyrjQDw&q=`abc%26%2B%E4%BD%A0%E5%A5%BD%08%E6%97%A5%E6%9C%AC%E6%96%87%E3%81%A8`&oq=abc%26%2B%E4%BD%A0%E5%A5%BD%08%E6%97%A5%E6%9C%AC%E6%96%87%E3%81%A8&gs_l=psy-ab.3..35i39k1l6.2170.2170.0.2308.4.2.0.0.0.0.0.0..1.0....0...1c.2.64.psy-ab..3.1.240.6...240.L7DaN_M8pP8

    ```Swift
    Google搜索引擎截取下的:  
    abc%26%2B%E4%BD%A0%E5%A5%BD%08%E6%97%A5%E6%9C%AC%E6%96%87%E3%81%A8

    我们自己按照规则得到的:
    abc%26%2b%e4%bd%a0%e5%a5%bd%08%e6%97%a5%e6%9c%ac%e6%96%87%e3%81%a8
    ```

    
在线转码工具:  
UTF8 Convert to Unicode Tools： https://www.branah.com/unicode-converter  
UTF8 Online Encode： https://www.browserling.com/tools/utf8-encode 

Reference：    
RFC3986协议：http://www.ietf.org/rfc/rfc3986.txt  
WiKi： https://en.wikipedia.org/wiki/Percent-encoding  
ruanyifeng： http://www.ruanyifeng.com/blog/2010/02/url_encoding.html

---