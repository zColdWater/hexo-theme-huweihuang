---
title: "[Basic] 常用的加密有哪些种"
catalog: true
toc_nav_num: true
date: 2019-01-07 10:55:30
subtitle: "在日常开发中我们接触到或者了解到的加密有哪些种。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/achievement-min.png"
tags:
- Basic
catagories:
- Basic
---

前言
=======
在日常的开发当中，我们经常会遇到各种Hash算法和加密算法，我们的主题不是谈论他们的实现方式，而是简单的知道他们的种类。


流行的Hash算法
=======

> 不可逆，常见的破解方法就是穷举法。

目前流行的 Hash 算法包括 `MD5`、`SHA-1` 和 `SHA-2`。

MD4（RFC 1320）是 MIT 的 Ronald L. Rivest 在 1990 年设计的，MD 是 Message Digest 的缩写。其输出为 128 位。MD4 已证明不够安全。  
MD5（RFC 1321）是 Rivest 于1991年对 MD4 的改进版本。它对输入仍以 512 位分组，其输出是 128 位。MD5 比 MD4 复杂，并且计算速度要慢一点，更安全一些。MD5 已被证明不具备"强抗碰撞性"。  
SHA （Secure Hash Algorithm）是一个 Hash 函数族，由 NIST（National Institute of Standards and Technology）于 1993 年发布第一个算法。目前知名的 SHA-1 在 1995 年面世，它的输出为长度 160 位的 hash 值，因此抗穷举性更好。SHA-1 设计时基于和 MD4 相同原理，并且模仿了该算法。SHA-1 已被证明不具"强抗碰撞性"。  
为了提高安全性，NIST 还设计出了 SHA-224、SHA-256、SHA-384，和 SHA-512 算法（统称为 SHA-2），跟 SHA-1 算法原理类似。SHA-3 相关算法也已被提出。  

可以看出，上面这几种流行的算法，它们最重要的一点区别就是"强抗碰撞性"。  

**意义:**  
1. 下载保证数据的完整性。  
2. 防止数据被篡改。


流行的对称加密算法
=======

> 下面信息来自维基百科。

对称密钥加密又称为对称加密、私钥加密、共享密钥加密，是密码学中的一类加密算法。这类算法在加密和解密时使用相同的密钥，或是使用两个可以简单地相互推算的密钥。事实上，这组密钥成为在两个或多个成员间的共同秘密，以便维持专属的通信联系。与公开密钥加密相比，要求双方获取相同的密钥是对称密钥加密的主要缺点之一。
常见的对称加密算法有`DES`、`3DES`、`AES`、`Blowfish`、`IDEA`、`RC5`、`RC6`。
对称加密的速度比公钥加密快很多，在很多场合都需要对称加密。




