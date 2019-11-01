---
title: "[C] 文本文件与二进制文件区别"
catalog: true
toc_nav_num: true
date: 2019-02-20 11:12:30
subtitle: "在C语言中文本文件与二进制文件区别"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- C
catagories:
- C
---

**问题: 文本文件和二进制文件的区别？请举例说明。**

广义上的二进制文件包括文本文件，这里讨论的是狭义上的二进制文件与文本文件的比较：

1. 能存储的数据类型不同     文本文件只能存储char型字符变量。     二进制文件可以存储char/int/short/long/float/……各种变量值。

2. 每条数据的长度     文本文件每条数据通常是固定长度的。以ASCII为例，每条数据(每个字符)都是1个字节。     二进制文件每条数据不固定。如short占两个字节，int占四个字节，float占8个字节……

3. 读取的软件不同     文本文件编辑器就可以读写。比如记事本、NotePad++、Vim等。     二进制文件需要特别的解码器。比如bmp文件需要图像查看器，rmvb需要播放器……

4. 操作系统对换行符('\n')的处理不同（不重要）     文本文件，操作系统会对'\n'进行一些隐式变换，因此文本文件直接跨平台使用会出问题。在Windows下，写入'\n'时，操作系统会隐式的将'\n'转换为"\r\n"，再写入到文件中；读的时候，会把“\r\n”隐式转化为'\n'，再读到变量中。在Linux下，写入'\n'时，操作系统不做隐式变换。


参考: https://www.zhihu.com/question/19971994/answer/36121103


