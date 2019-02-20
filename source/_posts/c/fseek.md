---
title: "[C] FSEEK"
catalog: true
toc_nav_num: true
date: 2019-02-20 10:11:30
subtitle: "移动文件流的读写位置"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/nature.jpg"
tags:
- C
catagories:
- C
---

`相关函数：rewind, ftell, fgetpos, fsetpos, lseek`

头文件：#include <stdio.h>

```C
定义函数：int fseek(FILE * stream, long offset, int whence);
```

函数说明：fseek()用来移动文件流的读写位置. 


1、参数`stream` 为已打开的文件指针,    
2、参数`offset` 为根据参数`whence` 来移动读写位置的位移数。参数 `whence` 为下列其中一种:   

    SEEK_SET 从距文件开头offset 位移量为新的读写位置. 
    SEEK_CUR 以目前的读写位置往后增加offset 个位移量.
    SEEK_END 将读写位置指向文件尾后再增加offset 个位移量. 当whence 值为SEEK_CUR 或
    SEEK_END 时, 参数offset 允许负值的出现.


**下列是较特别的使用方式：**
1) 欲将读写位置移动到文件开头时:`fseek(FILE *stream, 0, SEEK_SET)`;
2) 欲将读写位置移动到文件尾时:`fseek(FILE *stream, 0, 0SEEK_END)`;

`返回值`：`当调用成功时则返回0`, `若有错误则返回-1`, `errno` 会存放错误代码.

附加说明：`fseek()`不像`lseek()`会返回读写位置, 因此必须使用`ftell()`来取得目前读写的位置.

范例
```C
#include <stdio.h>
main()
{
    FILE * stream;
    long offset;
    fpos_t pos;
    stream = fopen("/etc/passwd", "r");
    fseek(stream, 5, SEEK_SET);
    printf("offset = %d\n", ftell(stream));
    rewind(stream);
    fgetpos(stream, &pos);
    printf("offset = %d\n", pos);
    pos = 10;
    fsetpos(stream, &pos);
    printf("offset = %d\n", ftell(stream));
    fclose(stream);
}
```

执行  
offset = 5  
offset = 0  
offset = 10  

