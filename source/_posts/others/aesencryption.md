---
title: "[Basic] AES加密"
catalog: true
toc_nav_num: true
date: 2019-03-12 01:34:30
subtitle: "了解AES对称加密使用"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- Basic
catagories:
- Basic
---

> AES加密

加密模式
=======

1. ECB
    
    ECB（电子密码本模式：Electronic codebook）是最简单的块密码加密模式，加密前根据加密块大小（如AES为128位）分成若干块，之后将每块使用相同的密钥单独加密，解密同理。

2. CBC(常用)

    CBC（密码分组链接：Cipher-block chaining）模式对于每个待加密的密码块在加密前会先与前一个密码块的密文异或然后再用加密器加密。第一个明文块与一个叫初始化向量的数据块异或。

3. CFB

    CBC（密文反馈:Cipher feedback）模式对于每个待加密的密码块在加密前会先与前一个密码块的密文异或然后再用加密器加密。第一个明文块与一个叫初始化向量的数据块异或。

4. OFB

    OFB（输出反馈：Output feedback）是先用块加密器生成密钥流（Keystream），然后再将密钥流与明文流异或得到密文流，解密是先用块加密器生成密钥流，再将密钥流与密文流异或得到明文，由于异或操作的对称性所以加密和解密的流程是完全一样的。


数据填充方式
=======

1. PKCS5Padding
    
    如果块长度是16，数据长度9，那么还差7个字节，就在后面补充7个0x07，如下所示  
    ```
    数据：  FF FF FF FF FF FF FF FF FF
    填充后：FF FF FF FF FF FF FF FF FF 07 07 07 07 07 07 07
    ```
    如果数据长度为16，仍然需要补充16个0x16

2. PKCS7Padding

    在AES加密的时候，同PKCS5Padding

3. ZeroPadding

    如字面意思，在数据后面填充0即可  
    ```
    数据：  FF FF FF FF FF FF FF FF FF
    填充后：FF FF FF FF FF FF FF FF FF 00 00 00 00 00 00 00
    ```

使用
=======

1. Python Python 实现采用Crpyto库，以下代码用lambda自己实现的pkcs5填充，仅供参考
    ```python
    # -*- coding:utf-8 -*-
    from Crypto.Cipher import AES
    from binascii import b2a_hex, a2b_hex
    import base64
    class aescrypt():
        def __init__(self, key, iv='1234567812345678'):
            self.key = key
            self.iv = iv
            self.mode = AES.MODE_CBC
            self.BS = AES.block_size
            # 补位
            self.pad = lambda s: s + (self.BS - len(s) % self.BS) * chr(self.BS - len(s) % self.BS)
            self.unpad = lambda s: s[0:-ord(s[-1])]
        def encrypt(self, text):
            text = self.pad(text)
            cryptor = AES.new(self.key, self.mode, self.iv)
            # 目前AES-128 足够目前使用
            ciphertext = cryptor.encrypt(text)
            # 把加密后的字符串转化为16进制字符串
            #return b2a_hex(ciphertext)
            # 返回加密字符串的base64值
            return base64.b64encode(ciphertext)
            # 解密后，去掉补足的空格用strip() 去掉
        def decrypt(self, text):
            cryptor = AES.new(self.key, self.mode, self.iv)
            plain_text = cryptor.decrypt(base64.b64decode(text))
            return self.unpad(plain_text.rstrip('\0'))
    if __name__ == '__main__':
        pc = aescrypt('1234567812345678')  # 初始化密钥 和 iv
        e = pc.encrypt("123456")  # 加密
        d = pc.decrypt(e)  # 解密
        print "加密:", e
        print "解密:", d
        print "长度:", len(d)
    ```

2. Javascript JS的加密采用了CryptoJS库，示例代码如下

    ECB 模式默认的是ZeroPadding

    ```javascript
    <script src="js/cryptojslib/rollups/aes.js"></script>
    <script src="js/cryptojslib/components/pad-zeropadding-min.js"></script>
    <script>
        var data = "123456";
        var key  = CryptoJS.enc.Latin1.parse('1234567812345678');
        var iv   = CryptoJS.enc.Latin1.parse('1234567812345678');
        //加密
        //如果不是和Java交互，而是和python或者其他交互，那么padding方式可以采用pkcs7(和pkcs5一样)
        var encrypted = CryptoJS.AES.encrypt(
                data,
                key,
                {iv:iv,mode:CryptoJS.mode.CBC,padding:CryptoJS.pad.ZeroPadding
                });
        console.log('encrypted: ' + encrypted) ;
    ```

3. C C语言里面依赖 mbedtls库(github)来提供aes加密，示例代码如下 

    CBC 模式默认的是PKCS5Padding填充

    ```C
    //main.c

    #include <stdio.h>
    #include <unistd.h>
    #include <dirent.h>
    #include <string.h>
    #include <stdlib.h>
    #include "fd_aes_helper.h"

    char KEY[16] = "0123456789012345";
    char IV[16] = "0123456789012345";
    fd_aes_init_key_iv(KEY, IV);
    
    unsigned char *iv = (unsigned char *)malloc(16);
    memcpy(iv, IV, 16);
    
    unsigned char *total_output = (unsigned char *)malloc(512);
    memset(total_output, '\0', 16);

    unsigned char* content1 = (unsigned char*)malloc(23);
    memset(content1, '\0', 16);
    memcpy(content1, "01234567890123456789012", 23);
    printf("content1:%s\n",content1);
    
    unsigned char* split1 = (unsigned char*)malloc(16);
    memset(split1, '\0', 16);
    memcpy(split1, content1, 16);
    printf("split1:%s\n",split1);
    
    unsigned char temp_output1[16] = {};
    printf("iv:%s \n",iv);
    int ret = fd_aes_encrypt(split1, temp_output1, 16, iv);
    if (!ret) {
        printf("ase failture! \n");
    }
    memcpy(total_output, temp_output1, 16);
    
    
    int val = 9;
    unsigned char* split2 = (unsigned char*)malloc(16);
    content1 += 16;
    memset(split2, val, 16);
    memcpy(split2, content1, 7);
    
    printf("split2:%s\n",split2);
    //printf("split2:%02X \n", (unsigned char)split2[7]);

    unsigned char temp_output2[16] = {};
    printf("iv:%s \n",iv);
    int ret1 = fd_aes_encrypt(split2, temp_output2, 16, iv);
    if (!ret1) {
        printf("ase failture! \n");
    }
    
    unsigned char *temp = total_output;
    temp += 16;
    memcpy(temp, temp_output2, 16);
    
    for (int i=0;i<32;i++) {
        printf("%02X", (unsigned char)total_output[i]);
    }
    printf("\n");
    ```

    ```C
    //fd_aes_helper.h
    #ifndef fd_aes_helper_h
    #define fd_aes_helper_h

    #include <stdbool.h>

    /**
    AES 加密

    @param in 待加密的内容
    @param out 输出的加密内容
    @param length 加密的数据长度(必须是16的整数倍)
    @param iv 加密向量
    @return 是否加密成功
    */
    bool fd_aes_encrypt(unsigned char *in, unsigned char *out, int length, unsigned char *iv);


    /**
    AES 初始化

    @param key 密钥
    @param iv 向量
    */
    void fd_aes_init_key_iv(const char *key, const char *iv);


    /**
    AES 填充IV

    @param iv 向量
    */
    void fd_aes_inflate_iv(unsigned char *iv);


    #endif /* fd_aes_helper_h */
    ```

    ```C
    #include "fd_aes_helper.h"
    #include "mbedtls/aes.h"
    #include <stdio.h>
    #include <string.h>

    static unsigned char KEY[16] = {0}; 
    static unsigned char IV[16] = {0};

    bool fd_aes_encrypt(unsigned char *in, unsigned char *out, int length, unsigned char *iv) {
        mbedtls_aes_context context;
        int ret0 = mbedtls_aes_setkey_enc(&context, (unsigned char *) KEY, 128);
        int ret1 = mbedtls_aes_crypt_cbc(&context, MBEDTLS_AES_ENCRYPT, length, iv, in, out); //加密
        if ((ret0 == 0) && (ret1 == 0)) {
            return true;
        }
        else {
            return false;
        }
    }

    void fd_aes_init_key_iv(const char *key, const char *iv) {
        memcpy(KEY, key, 16); // 密码16位 三种密钥大小(key size)：128 bits、192 bits和256 bits
        memcpy(IV, iv, 16);
    }

    void fd_aes_inflate_iv(unsigned char *iv) {
        memcpy(iv, IV, 16);
    }
    ```




工具
=======

1. 在线AES 加密解密工具 [链接](http://ctf.ssleye.com/caes.html)



参考:   
http://cclove.me/2017/10/09/AES%E5%8A%A0%E5%AF%86%E5%8E%9F%E7%90%86%E4%BB%A5%E5%8F%8A%E5%AE%9E%E7%8E%B0/