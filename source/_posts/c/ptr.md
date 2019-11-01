---
title: "[C] 二级指针"
catalog: true
toc_nav_num: true
date: 2019-10-08 16:00:30
subtitle: "二级指针的常见的作用"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- C
catagories:
- C
---

二级指针的作用应该是体现在函数调用方面。   
   
函数调用中:   
1. 如果想在被调函数中改变主调函数中指针所指向的地址存储的内容，请用一级指针
2. 如果想在被调函数中改变主调函数中指针的指向，请用二级指针
总之，要想改变内容，必须传入地址，

当内容为地址时，就要传入地址的地址，也就是二级地址

想仔细看的童鞋可以看这里: http://c.biancheng.net/cpp/html/85.html 


参考: https://www.jianshu.com/p/1435de8d1790 
