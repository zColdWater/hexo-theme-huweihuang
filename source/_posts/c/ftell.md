---
title: "[C] FTELL"
catalog: true
toc_nav_num: true
date: 2019-02-20 14:07:30
subtitle: "获取文件读写指针的当前位置"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- C
catagories:
- C
---

头文件：#include <stdio.h>

ftell() 函数用来获取文件读写指针的当前位置，其原型为：
```C
    long ftell(FILE * stream);
```
【参数】`stream` 为已打开的文件指针。

【返回值】成功则返回当前的读写位置，失败返回 -1。

对于二进制文件，则返回从文件开头到结尾的字节数。

对于文本文件，返回的数值可能没有实际意义，但仍然可以用来保存当前的读写位置，供 `fseek()` 函数使用

在随机方式存取文件时，由于文件位置频繁的前后移动，程序不容易确定文件的当前位置。使用`fseek`函数后再调用函数`ftell()`就能非常容易地确定文件的当前位置。

`ftell()` 经常和 `fseek()` 一起使用。例如，利用 `ftell()` 可以方便地获取一个文件的长：

```C
FILE *fp = fopen("demo.txt","rb");  
fseek(fp, 0L, SEEK_END);
len =ftell(fp)+1;
```
首先将文件的位置指针移到文件的末尾，然后调用函数 ftell() 获得当前位置相对于文件首的位移，该位移值等于文件所含字节数。



【实例】在读取文件中的字符时，不断读取文件读写指针的位置。
```C
#include<iostream.h>
#include<stdio.h>
void main(void)
{
    FILE* stream;
    long l;
    float fp;
    char s[81];
    char c;
    stream = fopen("fscanf.txt","w+");  // 打开
    if(stream == NULL) // 打开文件失败
    {
        printf("the file is opeaned error!\n");
    }
    else //输出信息
    {
        fprintf(stream,"%s %ld %f %c","a_string",6500,3.1415,'x');
        fseek(stream,0L,SEEK_SET); // 定位文件读写指针
        fscanf(stream,"%s",s);
        printf("%ld\n",ftell(stream));
        fscanf(stream,"%ld",&l);
        printf("%ld\n",ftell(stream));
        fscanf(stream,"%f",&fp);
        printf("%ld\n",ftell(stream));
        fscanf(stream," %c",&c);
        printf("%ld\n",ftell(stream));
   
        fclose(stream);  // 关闭
    }
}
```
运行结果：  
8  
13  
22  
24  

程序首先是新建一个文件，然后使用 `fprintf()` 函数写入一些数据，再使用 `fseek()` 函数把文件的读取写指针定位到文件的开头，开始读取文件数据，此时文件的读/写指针会不断地向后移动，程序每读取一次数据就获取一次文件指针的位置并输出。
