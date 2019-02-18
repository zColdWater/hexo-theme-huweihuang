---
title: "[C] Memset"
catalog: true
toc_nav_num: true
date: 2019-02-18 13:00:30
subtitle: "Memset的使用方法"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/knowledge-min.png"
tags:
- C
catagories:
- C
---

> 介绍C标准库 Memset 方法的使用

头文件：#include <string.h>

`memset()` 函数用来将指定内存的前n个字节设置为特定的值，其原型为：  
    `void * memset( void * ptr, int value, size_t num );`


参数说明：  
`ptr` 为要操作的内存的指针。  
`value` 为要设置的值。你既可以向 `value` 传递 `int` 类型的值，也可以传递 `char` 类型的值，`int` 和 `char` 可以根据 `ASCII` 码相互转换。  
`num` 为 `ptr` 的前 `num` 个字节，`size_t` 就是`unsigned int`。  


【函数说明】memset() 会将 ptr 所指的内存区域的前 num 个字节的值都设置为 value，然后返回指向 ptr 的指针。


`memset()` 可以将一段内存空间全部设置为特定的值，所以经常用来初始化字符数组。例如：

例子: 
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main()
{
    // 不可以声明为 char *str = "http://c.biancheng.net";
    char str[] = "http://c.biancheng.net";
    memset(str, '-', 7);
    puts(str);
    system("pause");
    return EXIT_SUCCESS;
}
``` 
执行结果：
-------c.biancheng.net


