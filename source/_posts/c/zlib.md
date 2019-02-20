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


**关于 gzip, zlib 和 zip**

`gzip`是UNIX下的一种数据格式. `gzip`是在`zlib`之上，包了一层，在头和尾添加了一些额外的信息. `gzip`是一种文件压缩工具（或该压缩工具产生的压缩文件格式），它的设计目标是处理单个的文件。`gzip`在压缩文件中的数据时使用的就是`zlib`。为了保存与文件属性有关的信息，`gzip`需要在压缩文件`（.gz）`中保存更多的头信息内容，而`zlib`不用考虑这一点。但`gzip`只适用于单个文件，所以我们在`UNIX/Linux`上经常看到的压缩包后缀都是`.tar.gz`或`.tgz`，也就是先用`tar`把多个文件打包成单个文件，再用`gzip`压缩的结果。  

`zlib` 是一个开源库, 提供了在内存中压缩和解压的函数. `zlib` 的设计目标是处理单纯的数据.
`zip` 只是一种数据结构，跟`rar`同级别的。 `zip`是适用于压缩多个文件的格式（相应的工具有`PkZip`和`WinZip`等），因此，`zip`文件还要 进一步包含文件目录结构的信息，比`gzip`的头信息更多。但需要注意，`zip`格式可采用多种 压缩算法，我们常见的`zip`文件大多不是用`zlib`的算法压缩的，其压缩数据的格式与`gzip`大 不一样。  

`gzip`, `zlib`以及图形格式`png`，使用的压缩算法都是`deflate`算法。  
  
`gzip` 对于要压缩的文件，首先使用`LZ77`算法的一个变种进行压缩，对得到的结果再使用`Huffman`编码的方法进行压缩.

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

> zlib的核心数据结构是z_stream，用来表示比特流，同时用于deflate或者inflate过程。以deflate过程为例，z_stream必须使用deflateInit()函数初始化。但是值得注意的是，在调用deflateInit()之前，需要先把z_stream数据结构中的三个属性初始化，分别是：zalloc, zfree和opaque。这三个参数是给zlib的应用程序制定内存分配器用的，如果想让zlib使用系统默认的内存分配器，将这三个参数设为无效值即可，比如Z_NULL。

> 当z_stream不再使用的时候，要使用deflateEnd()来释放内部的数据结果。


**deflateInit2:**

```C
z_streamp strm: z_stream 指针；

int level: 压缩等级，必需为 Z_DEFAULT_COMPRESSION 或者 0 ~ 9 的整数，1为最快，9为最大限度压缩，0为不压缩，数字越大越耗时；

int method: 压缩算法，只支持 Z_DEFLATED；

int windowBits: 历史缓冲区最大尺寸，值为 2^windowBits, windowBits 的值为 8~15 时，deflate() 方法生成 zlib 格式的数据，当 windowBits 为 31 时 deflate() 方法生成 gzip 格式。当取值为 -15 ~ -8 时，deflate() 生成纯 deflate 算法压缩数据（不包含 zlib 和 gzip 格式头和尾）

int strategy: 用于调整压缩算法，一般使用 Z_DEFAULT_STRATEGY

ZEXTERN int ZEXPORT deflateInit2 OF((z_streamp strm,
                                     int  level,
                                     int  method,
                                     int  windowBits,
                                     int  memLevel,
                                     int  strategy));
```

以生成 gzip 格式输出为例：

```C
OSStatus status = deflateInit2(&zStream, Z_DEFAULT_COMPRESSION, Z_DEFLATED, 31, MAX_MEM_LEVEL, Z_DEFAULT_STRATEGY);
    
if (status != Z_OK) {
    return nil;
}
```


**deflate:**

> deflate()函数是deflate过程中的核心函数，经常需要多次调用，这个函数带两个参数z_stream和flush，使用的时候有以下注意点：

1) 调用deflate()前要初始化z_stream的以下属性：
    1) `next_in`， 下一个输入字节之所在
    2) `avail_in`， next_in还有多少字节可以输入
    3) `next_out`， 能够存储下一个输出字节的起始地址
    4) `avail_out`， next_out还有多少字节空间可以用来保存输出字节
2) 应用程序必须确保deflate()可以从`next_in`中读到数据，或者从`next_out`中写入数据，否则会返回`Z_STREAM_ERROR`
3) 如果deflate()无法读取或者写入数据，会返回`Z_BUF_ERROR`
4) 正常的情况下，当`deflate()`处理了一些数据后会返回`Z_OK`
5) 当deflate()读取了所有输入数据，然后这些数据都写入输出了之后，deflate()会返回`Z_STREAM_END`。
6) 通过flush参数可以控制deflate()的输出过程
    1) 设为`Z_NO_FLUSH`的话，输出多少数据由deflate()决定
    2) 设为`Z_FULL_FLUSH`的话，deflate()会把所有的处理好的数据输出，并且重置状态。经常使用
    3) `Z_FULL_FLUSH`会影响性能。
    4) 设为`Z_FINISH`的话，告诉deflate()所有输入数据都已经提供，可以结束处理了。deflate()会把剩余的结果输出，然后结束处理过程。之后，只能调用deflateReset来重置内部状态，或者deflateEnd来释放内部状态。
    5) 其他选项，比如：`Z_SYNC_FLUSH`，`Z_PARTIAL_FLUSH`，`Z_BLOCK`属于高级用法，就不描述了，可以参考文档
    6) 有一点需要注意，当输出空间不足，也就是`avail_out`为空的时候，再次调用deflate()的时候必须提供更多输出空间，以及保持`flush`标志不变。
    与`defalte`类似，`inflate`也有对应的：`inflateInit()`，`inflate()`和`inflateEnd()`。

7) FLUSH的类型  
    #define Z_NO_FLUSH      0   
    #define Z_PARTIAL_FLUSH 1  
    #define Z_SYNC_FLUSH    2   
    #define Z_FULL_FLUSH    3   
    #define Z_FINISH        4   
    #define Z_BLOCK         5   
    #define Z_TREES         6   

    Normally the parameter flush is set to Z_NO_FLUSH, which allows deflate to decide how much data to accumulate before producing output, in order to maximize compression.

    If the parameter flush is set to Z_SYNC_FLUSH, all pending output is flushed to the output buffer and the output is aligned on a byte boundary, so that the decompressor can get all input data available so far. Flushing may degrade compression for some compression algorithms and so it should be used only when necessary. This completes the current deflate block and follows it with an empty stored block that is three bits plus filler bits to the next byte, followed by four bytes (00 00 ff ff).

    If flush is set to Z_PARTIAL_FLUSH, all pending output is flushed to the output buffer, but the output is not aligned to a byte boundary. All of the input data so far will be available to the decompressor, as for Z_SYNC_FLUSH.
    This completes the current deflate block and follows it with an empty fixed codes block that is 10 bits long. This assures that enough bytes are output in order for the decompressor to finish the block before the empty fixed codes block.

    If flush is set to Z_BLOCK, a deflate block is completed and emitted, as for Z_SYNC_FLUSH, but the output is not aligned on a byte boundary, and up to seven bits of the current block are held to be written as the next byte after the next deflate block is completed. In this case, the decompressor may not be provided enough bits at this point in order to complete decompression of the data provided so far to the compressor. It may need to wait for the next block to be emitted. This is for advanced applications that need to control the emission of deflate blocks.

    If flush is set to Z_FULL_FLUSH, all output is flushed as with Z_SYNC_FLUSH, and the compression state is reset so that decompression can restart from this point if previous compressed data has been damaged or if random access is desired. Using Z_FULL_FLUSH too often can seriously degrade compression.

    If deflate returns with avail_out == 0, this function must be called again with the same value of the flush parameter and more output space (updated avail_out), until the flush is complete (deflate returns with non-zero avail_out). In the case of a Z_FULL_FLUSH or Z_SYNC_FLUSH, make sure that avail_out is greater than six to avoid repeated flush markers due to avail_out == 0 on return.

除了`deflate`和`inflate`对应的函数接口以外，`zlib`还提供了一些封装了这些接口的实用函数，比如：`compress()`和`uncompress()`。这些函数简化了`deflate`和`inflate`函数接口的使用。


下面是使用ZLIB来压缩成GZIP
```C
#define CHUNK 16384  // 16 * 1024

int def(FILE *source, FILE *dest)
{
    int ret, flush;
    unsigned have;
    z_stream strm;
    unsigned char in[CHUNK];
    unsigned char out[CHUNK];
    
    /* allocate deflate state */
    strm.zalloc = Z_NULL;
    strm.zfree = Z_NULL;
    strm.opaque = Z_NULL;
    ret = deflateInit2(&strm, Z_BEST_COMPRESSION, Z_DEFLATED, (15 + 16), 8,
                       Z_DEFAULT_STRATEGY);
    if (ret != Z_OK)
        return ret;
    
    /* compress until end of file */
    do {
        strm.avail_in = fread(in, 1, CHUNK, source);
        if (ferror(source)) {
            (void)deflateEnd(&strm);
            return Z_ERRNO;
        }
        flush = feof(source) ? Z_FINISH : Z_SYNC_FLUSH; //Z_NO_FLUSH;
        
        strm.next_in = in;
        
        /* run deflate() on input until output buffer not full, finish
         compression if all of source has been read in */
        do {
            strm.avail_out = CHUNK;
            strm.next_out = out;
            ret = deflate(&strm, flush);    /* no bad return value */
            assert(ret != Z_STREAM_ERROR);  /* state not clobbered */
            have = CHUNK - strm.avail_out;
            if (fwrite(out, 1, have, dest) != have || ferror(dest)) {
                (void)deflateEnd(&strm);
                return Z_ERRNO;
            }
        } while (strm.avail_out == 0);
        assert(strm.avail_in == 0);     /* all input will be used */
        
        /* done when last data in file processed */
    } while (flush != Z_FINISH);
    assert(ret == Z_STREAM_END);        /* stream will be complete */
    
    /* clean up and return */
    (void)deflateEnd(&strm);
    return Z_OK;
}
```


下面是使用ZLIB来解压缩成GZIP
```C
int inf(FILE *source, FILE *dest)
{
    int ret;
    unsigned have;
    z_stream strm;
    unsigned char in[CHUNK];
    unsigned char out[CHUNK];
    
    /* allocate inflate state */
    strm.zalloc = Z_NULL;
    strm.zfree = Z_NULL;
    strm.opaque = Z_NULL;
    strm.avail_in = 0;
    strm.next_in = Z_NULL;
    ret = inflateInit(&strm);
    if (ret != Z_OK)
        return ret;
    
    /* decompress until deflate stream ends or end of file */
    do {
        strm.avail_in = fread(in, 1, CHUNK, source);
        if (ferror(source)) {
            (void)inflateEnd(&strm);
            return Z_ERRNO;
        }
        if (strm.avail_in == 0)
            break;
        strm.next_in = in;
        
        /* run inflate() on input until output buffer not full */
        do {
            strm.avail_out = CHUNK;
            strm.next_out = out;
            ret = inflate(&strm, Z_NO_FLUSH);
            assert(ret != Z_STREAM_ERROR);  /* state not clobbered */
            switch (ret) {
                case Z_NEED_DICT:
                    ret = Z_DATA_ERROR;     /* and fall through */
                case Z_DATA_ERROR:
                case Z_MEM_ERROR:
                    (void)inflateEnd(&strm);
                    return ret;
            }
            have = CHUNK - strm.avail_out;
            if (fwrite(out, 1, have, dest) != have || ferror(dest)) {
                (void)inflateEnd(&strm);
                return Z_ERRNO;
            }
        } while (strm.avail_out == 0);
        
        /* done when inflate() says it's done */
    } while (ret != Z_STREAM_END);
    
    /* clean up and return */
    (void)inflateEnd(&strm);
    return ret == Z_STREAM_END ? Z_OK : Z_DATA_ERROR;
}
```


