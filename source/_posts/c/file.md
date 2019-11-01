---
title: "[C] FILE"
catalog: true
toc_nav_num: true
date: 2019-02-20 10:11:30
subtitle: "在C语言中如何简单的操作文件"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- C
catagories:
- C
---

文件流
=======

> 如果你想操作一个文件流，你需要使用下面函数。

文件打开(fopen函数)函数用来打开一个文件，它的原型为：
```C
/// filename为文件名（包括文件路径)
/// mode为打开方式
函数原型：FILE * fopen(const char * path,const char * mode);
```
会获取文件信息，包括文件名、文件状态、当前读写位置等，并将这些信息保存到一个`FILE`类型的结构体变量中，然后将该变量的地址返回。


Mode模式的种类:   
| 打开方式	     | 说明           |
| ------------- |:-------------:|
| r      | 以只读方式打开文件，只允许读取，不允许写入。该文件必须存在。 |
| r+      | 以读/写方式打开文件，允许读取和写入。该文件必须存在。      |
| rb+ | 以读/写方式打开一个二进制文件，允许读/写数据。      | 
| rt+      | 以读/写方式打开一个文本文件，允许读和写。 |
| w      | 以只写方式打开文件，若文件存在则长度清为0，即该文件内容消失，若不存在则创建该文件。      |
| w+ | 以读/写方式打开文件，若文件存在则文件长度清为零，即该文件内容会消失。若文件不存在则建立该文件。      | 
| a      | 以追加的方式打开只写文件。若文件不存在，则会建立该文件，如果文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留（EOF符保留)。 |
| a+      | 以追加方式打开可读/写的文件。若文件不存在，则会建立该文件，如果文件存在，则写入的数据会被加到文件尾后，即文件原先的内容会被保留（原来的EOF符 不保留)。      |
| wb | 以只写方式打开或新建一个二进制文件，只允许写数据。      | 
| wb+      | 以读/写方式打开或建立一个二进制文件，允许读和写。 |
| wt+      | 以读/写方式打开或建立一个文本文件，允许读写。      |
| at+ | 以读/写方式打开一个文本文件，允许读或在文本末追加数据。      | 
| ab+      | 以读/写方式打开一个二进制文件，允许读或在文件末追加数据。 |

例子:

```C
表示以“只读”方式打开当前目录下的 demo.txt 文件，并使 fp 指向该文件，这样就可以通过 fp 来操作 demo.txt 了。fp 通常被称为文件指针。

FILE *fp = fopen("demo.txt", "r");


表示以二进制方式打开 D 盘下的 demo.txt 文件，允许读和写。
FILE *fp = fopen("D:\\demo.txt","rb");

```

文件关闭(fclose函数) 文件一旦使用完毕，应该用fclose()函数把文件关闭，以释放相关资源，避免数据丢失。fclose() 的原型为：

```C
int fclose(FILE *fp);
```
fp 为文件指针。例如：
```C
fclose(fp);
```

文件正常关闭时，fclose() 的返回值为0，如果返回非零值则表示有错误发生。

操作文件
=======

**写文件函数(将数据流写入文件中)**

相关函数：fopen, fread, fseek, fscanf

头文件：#include <stdio.h>

定义函数：`size_t fwrite(const void * ptr, size_t size, size_t nmemb, FILE * stream)`;

函数说明：`fwrite()`用来将数据写入文件流中. 参数`stream` 为已打开的文件指针, 参数`ptr` 指向欲写入的数据地址, 总共写入的字符数以参数`size*nmemb` 来决定. `Fwrite()`会返回实际写入的`nmemb` 数目.

返回值：返回实际写入的`nmemb`数目.

代码例子
```C
返回值：返回实际写入的nmemb 数目.

范例
#include <stdio.h>
#define set_s(x, y) {strcoy(s[x].name, y); s[x].size = strlen(y);}
#define nmemb 3
struct test
{
    char name[20];
    int size;
} s[nmemb];

main()
{
    FILE * stream;
    set_s(0, "Linux!");
    set_s(1, "FreeBSD!");
    set_s(2, "Windows2000.");
    stream=fopen("/tmp/fwrite", "w");
    fwrite(s, sizeof(struct test), nmemb, stream);
    fclose(stream);
}

执行 参考fread ().
```

**读文件函数(从文件流读取数据)**
相关函数：fopen, fwrite, fseek, fscanf

头文件：#include <stdio.h>

定义函数：`size_t fread(void * ptr, size_t size, size_t nmemb, FILE * stream);`

函数说明：fread()用来从文件流中读取数据. 

参数stream 为已打开的文件指针, 参数ptr 指向欲存放读取进来的数据空间, 读取的字符数以参数size*nmemb 来决定. Fread()会返回实际读取到的nmemb 数目, 如果此值比参数nmemb 来得小, 则代表可能读到了文件的尾或有错误发生, 这时必须用feof()或ferror()来决定发生什么情况.

返回值：返回实际读取到的nmemb数目。

```C
范例
#include <stdio.h>
#define nmemb 3
struct test
{
    char name[20];
    int size;
} s[nmemb];

main()
{
    FILE * stream;
    int i;
    stream = fopen("/tmp/fwrite", "r");
    fread(s, sizeof(struct test), nmemb, stream);
    fclose(stream);
    for(i = 0; i < nmemb; i++)
        printf("name[%d]=%-20s:size[%d]=%d\n", i, s[i].name, i, s[i].size);
}

执行
name[0]=Linux! size[0]=6
name[1]=FreeBSD! size[1]=8
name[2]=Windows2000 size[2]=11
```

注意事项
=======

1. 文件打开方式由r、w、a、t、b、+ 六个字符拼成，各字符的含义是：

    r(read)：`读`

    w(write)：`写`

    a(append)：`追加`

    t(text)：`文本文件，可省略不写`

    b(banary)：`二进制文件`

    +：`读和写`

2. 如果没有`“b”`字符，文件以文本方式打开。

3. 凡用`“r”`打开一个文件时，`该文件必须已经存在`。

4. 在打开一个文件时，如果出错，`fopen`将返回一个空指针值`NULL`。在程序中可以用这一信息来判别是否完成打开文件的工作，并作相应的处理。因此常用以下程序段打开文件：
    ```C
    if( (fp=fopen("D:\\demo.txt","rb") == NULL ){
        printf("Error on open D:\\demo.txt file!");
        getch();
        exit(1);
    }
    ```
    这段程序的意义是，如果返回的指针为空，表示不能打开D盘根目录下的 demo.txt 文件，并给出提示信息“error on open D:\\demo.txt file!”。第3行getch()的功能是从键盘输入一个字符，但不在屏幕上显示。在这里，该行的作用是等待，只有当用户从键盘敲任一键时，程序才继续执行，因此用户可利用这个等待时间阅读出错提示。敲键后执行exit(1)退出程序。

5. 把一个文本文件读入内存时，要将ASCII码转换成二进制码，而把文件以文本方式写入磁盘时，也要把二进制码转换成ASCII码，因此文本文件的读写要花费较多的转换时间。对二进制文件的读写不存在这种转换。
