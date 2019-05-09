---
title: "[C] RSA 加密解密"
catalog: true
toc_nav_num: true
date: 2019-05-08 16:00:30
subtitle: "如何使用mbedtls库RSA加密解密。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- C
catagories:
- C
---

> 如何在C中使用mbedtls库进行RSA加密解密。

前言
=======

> 在C中我们使用 mbedtls 提供给我们的RSA加密方法来进行标准的RSA的加密解密，这里需要说的一点是，mbedtls 方法加密后得到的是一个字节流，需要进行 Base64 编码才会变成大家常见到的加密后的格式。

注意: 

1. RSA加密后的结果每次都是随机的，也就是变化的。

2. 生成RSA密钥的时候是可以选择带密码的，当然也可以不使用密码（大部分都是无密码的 省事），还记得我们生成.ssh文件夹下的RSA密钥对，让我们输入密码，我们一般不会去输入，一路回车创建好，如果我们设置了密码，每次进行SSH连接的时候都会提示让我们输入密码，这样确实有些麻烦。

3. mbedtls生成的加密数据是hex value呈现，解密也是需要传入hex value。 如果需要以字符串传递需要base64.



例子一:
=======

待加密内容 ---> RSA加密 ----> 加密结果(HexValue) ---> RSA解密 ---> 解密结果(待加密内容)

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <mbedtls/pk.h>
#include <mbedtls/rsa.h>
#include <mbedtls/entropy.h>
#include <mbedtls/ctr_drbg.h>

int main()
{

    int ret=0;

    // random data generator
    mbedtls_entropy_context entropy;
    mbedtls_entropy_init( &entropy );

    // randomness with seed
    mbedtls_ctr_drbg_context ctr_drbg;
    char *personalization = "My RSA demo";
    mbedtls_ctr_drbg_init( &ctr_drbg );

    ret = mbedtls_ctr_drbg_seed( &ctr_drbg , mbedtls_entropy_func, &entropy,
                                (const unsigned char *) personalization,
                                strlen( personalization ) );
    if( ret != 0 )
    {
        // ERROR HANDLING CODE FOR YOUR APP
    }
    mbedtls_ctr_drbg_set_prediction_resistance( &ctr_drbg,
                                               MBEDTLS_CTR_DRBG_PR_ON );
    ////////////////////////////////////////////////////////////////////////
    
    mbedtls_pk_context pk;
    mbedtls_pk_init( &pk );
    
    unsigned char to_encrypt[]="Yongpeng.Zhu";
    unsigned char to_decrypt[MBEDTLS_MPI_MAX_SIZE];
    const unsigned char pub_key[]=
    "-----BEGIN PUBLIC KEY-----\r\n"
    "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC9Jc728q/SZskvJc28q7wJjN2l\r\n"
    "rY8kV6cDWTtw/TikPugRdnaCdU/hBfahMRsh0d6ccibV3pZe/Hbug5l1yxyykOSe\r\n"
    "gVJx/qWI2FU8LbLHK7XcSzR1n/CogzhVYIHTu7pxKYt2unvmKB+gThUYduHW68xp\r\n"
    "iba04vDfCw7KvSlTtQIDAQAB\r\n"
    "-----END PUBLIC KEY-----\r\n";


    const unsigned char prv_pwd[]="password";
    const unsigned char prv_key[]=
    "-----BEGIN RSA PRIVATE KEY-----\r\n"
    "MIICXgIBAAKBgQC9Jc728q/SZskvJc28q7wJjN2lrY8kV6cDWTtw/TikPugRdnaC\r\n"
    "dU/hBfahMRsh0d6ccibV3pZe/Hbug5l1yxyykOSegVJx/qWI2FU8LbLHK7XcSzR1\r\n"
    "n/CogzhVYIHTu7pxKYt2unvmKB+gThUYduHW68xpiba04vDfCw7KvSlTtQIDAQAB\r\n"
    "AoGAYp8jEZmqWR8kyQOCCVzV13juXKNpHj7hoxpUpu4xKVpvcCN/WThHpQGR/av4\r\n"
    "BKND2fifDSZY6z/h1y0gx81WsVBPaLr5fVNgR4CLGH7kjCjigUtjIzJ7UpI9+ZCh\r\n"
    "YWMHyjBs1UL0QkhPZe9h1lQQetvfB0lKNuVVltoySyJQP0ECQQD/vssAPrqGR4G5\r\n"
    "HfgVvhFWWdQgnl6DuOQeRza+LBbM/ZPdiPfhhcpIpM65bFJ1Wigta4aiq64MLOYz\r\n"
    "MlNz0MRxAkEAvVYJAd2hsVZfR0rL8PYLHbeYMhrRIf9gYgfu9a5MM440S/rIB0Gh\r\n"
    "moc7CyC0CqJHYKpL8RQHx13KeJKf/fYVhQJBAPul2oyQLOvKWuwzgBSs5NRqKaA7\r\n"
    "FVdZzCW6/zPboEfvUNtRVlB0XJpkiQHNg8nzf8tJnb5dXjKez5ka8SDqERECQQCu\r\n"
    "dqnEG1qUE1emVMjJ1550mqlWehl9L1m72z2ZCyvSUdXksUhCT3q+7p88aL0eE1yc\r\n"
    "OS/TDDcCwW0BX3KnzGsVAkEAtn+JnKHFtHNBN+GgWSdvibeRTZE10GqomsYTUvWR\r\n"
    "Zu4Zb+zMdf/7HCspxo50dpZN6s3WfGUA0fNqYKi3NCsBPg==\r\n"
    "-----END RSA PRIVATE KEY-----\r\n";

    if( ( ret = mbedtls_pk_parse_public_key( &pk, pub_key, sizeof(pub_key) ) ) != 0 )
    {
        printf( " failed\n ! mbedtls_pk_parse_public_keyfile returned -0x%04x\n", -ret );
        return -1;
    }

    unsigned char buf[MBEDTLS_MPI_MAX_SIZE];
    size_t olen = 0;

    printf( "\nGenerating the encrypted value\n" );
    fflush( stdout );

    if( ( ret = mbedtls_pk_encrypt( &pk, to_encrypt, sizeof(to_encrypt),
                                   buf, &olen, sizeof(buf),
                                   mbedtls_ctr_drbg_random, &ctr_drbg ) ) != 0 )
    {
        printf( " failed\n ! mbedtls_pk_encrypt returned -0x%04x\n", -ret );
        return -1;
    }



    for(int idx=0; idx<strlen(buf); printf("%02x", buf[idx++]));
    printf ("\n");
    mbedtls_pk_context pk1;
    mbedtls_pk_init(&pk1);

    
    if( ( ret = mbedtls_pk_parse_key( &pk1, prv_key, sizeof(prv_key), prv_pwd, strlen(prv_pwd) ) ) != 0 )
    {
        printf( " failed\n ! mbedtls_pk_parse_keyfile returned -0x%04x\n", -ret );
        return -1;
    }

    unsigned char result[MBEDTLS_MPI_MAX_SIZE];
//    olen = 0;

    printf( "\nGenerating the decrypted value" );
    fflush( stdout );

    if( ( ret = mbedtls_pk_decrypt( &pk1, buf, olen, result, &olen, sizeof(result),
                                   mbedtls_ctr_drbg_random, &ctr_drbg ) ) != 0 )
    {
        printf( " failed\n! mbedtls_pk_decrypt returned -0x%04x\n", -ret );
        return -1;
    }
    else
    {

        fflush( stdout );
        printf("\n\n%s----------------\n\n", result);
    }

    return 0;
}
```



例子二:
=======

待加密内容 ---> RSA加密 ---> 加密结果(HexValue) ---> Base64Encode ---> Result(String) ---> Base64Decode ---> 加密结果(HexValue) ---> RSA解密 ---> 解密结果(待加密内容)

```C

```
