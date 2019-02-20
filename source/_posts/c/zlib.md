---
title: "[C] ZLIB"
catalog: true
toc_nav_num: true
date: 2019-02-20 14:47:30
subtitle: "如何简单使用ZLIB"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/knowledge-min.png"
tags:
- C
catagories:
- C
---

> zlib手册地址: https://www.zlib.net/manual.html


前言
=======

**我们先了解下 tar gz zip 7z 有什么区别?**

1) tar是Linux常见的一种归档文件（原生不包括压缩功能）gzip是gnu/Linux的一种压缩文件工具，算法是基于 DEFLATE，文件是gz，可以和tar组合。

2) zip是一种规范开放的压缩文件，算法不定，但主要是用 DEFLATErar的算法专有，但发行时附送解码器允许解码器再开发，编码器专有7zip和zip差不多，算法不定，主要用bzip2和lzma，而且完全开源。

3) zip和7z更像是压缩容器，因为算法不是格式固定，允许支持其他压缩算法


**zip vs gzip:**

1) zip和gzip(gz)不兼容，虽然它们都是使用相同的deflate压缩算法
2) zip更像一个打包器，能把多个多件放到一个zip中；gzip一次只对一个文件压缩，通常与tar命令一起用


很多压缩方式基于deflate算法的压缩库。实际上还存在别的算法：bzip2、LZMA等，他们有的甚至是完胜deflate。但很可惜，在互联网中要更替一种标准是非常困难的。deflate已经广泛运用到web的方方面面，难以取代。
所以还是学习deflate先吧。并且，阅读zlib库最佳。

zlib基本介绍
=======

**zlib 的z_stream结构 如下:**
```C
// 压缩控制器
typedef struct z_stream_s {
    z_const Bytef  *next_in;// 将要压缩数据的首地址
    uInt                 avail_in;// 待压缩数据next_in的长度
    uLong             total_in;// 当前已读取多少输入字节

    Bytef    *next_out;// 指向输出缓冲区，只需要在一开始赋值一次
    uInt     avail_out;// 输出缓冲区next_out的剩余空间 
    uLong    total_out;// 当前已输出多少输入字节

    z_const char   *msg;// 存放最近的错误信息，NULL表示没有错误
    struct internal_state FAR *state;// 暂时不用管这个

    alloc_func        zalloc;// 初始化要设置为 Z_NULL 压缩过程会分配新内存（internal state），用户可以设置自己的alloc函数给z_stream
    free_func         zfree;// 初始化要设置为 Z_NULL   释放内存
    voidpf              opaque;// 初始化要设置为 Z_NULL  zalloc、zfree的第一个参数，默认设0即可

    int                data_type;// 表示数据类型，文本或者二进制 
    uLong             adler;//未压缩数据的检验和 （Adler-32 or CRC-32)
    uLong             reserved;//reserved for future use
}  z_stream;
```


**zlib常用函数**

| 函数名        | 介绍           |
| ------------- |:-------------:|
| deflateInit      | 参数比较少,里面的实现其实是调用的deflateInit2 |
| deflateInit2      | 压缩初始化的基础函数,有很多参数,下面会重点介绍 |
| deflate | 压缩函数 |
| deflateEnd | 压缩完成以后,释放空间,但是注意,仅仅是释放deflateInit中申请的空间,自己申请的空间还是需要自己释放 |
| inflateInit | 解压初始化函数,内部调用的inflateInit2. |
| inflateInit2 | 解压初始化的基础函数.后面重点介绍. |
| infalte | 解压函数 |
| inflateEnd | 同deflateEnd作用类似 |
| compress | 全部附加选项默认压缩,内部调用compress2 |
| compress2 | 带level的压缩方式. |
| uncompress | 解压缩 |

上面是常用的一些函数，我们会着重讲解几个函数，因为小编能力有限也只接使用过几个API。


zlib常用函数
=======

