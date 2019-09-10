---
title: "[C] FFLUSH"
catalog: true
toc_nav_num: true
date: 2019-02-20 14:07:30
subtitle: "在C语言中如何使用FFLUSH"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- C
catagories:
- C
---

头文件：#include<stdio.h>

fflush()不是标准库文件，经笔者测试，VC6.0完美支持，GCC（GCC4.6.2）不支持。

fflush()用于清空文件缓冲区，如果文件是以写的方式打开 的，则把缓冲区内容写入文件。其原型为：
```C
    int fflush(FILE* stream);
```

【参数】stream为文件指针。

【返回值】成功返回0，失败返回EOF，错误代码存于errno 中。指定的流没有缓冲区或者只读打开时也返回0值。

fflush()也可用于标准输入（stdin）和标准输出（stdout），用来清空标准输入输出缓冲区。

stdin 是 standard input 的缩写，即标准输入，一般是指键盘；标准输入缓冲区即是用来暂存从键盘输入的内容的缓冲区。stdout 是 standard output 的缩写，即标准输出，一般是指显示器；标准输出缓冲区即是用来暂存将要显示的内容的缓冲区。



【实例】把输入流与一个缓冲区关联，然后清空缓冲区。

```C
#include <stdio.h> 
char inbuf[BUFSIZ]; 
int main(void) 
{ 
    char a[100];
    setbuf(stdin, inbuf);
    printf("input a string =");
    scanf("%s",a);
    /*往缓冲区写入数据 */ 
    puts(inbuf);
    if(0 ==fflush(inbuf)) /*清空文件缓冲区*/
    {
        puts(inbuf);
    }
    puts(inbuf);
    return 0; 
}
```
运行结果：  
input a string = qwe  
qwe

程序先把缓冲区与输入流关联，这样输入的内容就在缓冲区中是可见的。提示用户输入一个字符串，用户输入后， 该字符串会在缓冲区中保存，这时我们可以使用puts函数输出一 遍，然后使用fflush函数清空缓冲区，再次输出结果为空。