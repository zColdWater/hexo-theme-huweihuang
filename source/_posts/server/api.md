---
title: "[Backend] API接口设计"
catalog: true
toc_nav_num: true
date: 2019-11-01 16:44:30
subtitle: "防止参数篡改和重放攻击"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Backend
catagories:
- Backend
---

API重放攻击（`Replay Attacks`）又称重播攻击、回放攻击。他的原理就是把之前窃听到的数据原封不动的重新发送给接收方。**HTTPS并不能防止这种攻击**，虽然传输的数据是经过加密的，窃听者无法得到数据的准确定义，但是可以从请求的接收方地址分析出这些数据的作用。比如用户登录请求时攻击者虽然无法窃听密码，但是却可以截取加密后的口令然后将其重放，从而利用这种方式进行有效的攻击。

> 所谓重放攻击就是攻击者发送一个目的主机已接收过的包，来达到欺骗系统的目的，主要用于身份认证过程，重放攻击是计算机世界黑客常用的攻击方式之一。

一次HTTP请求，从请求方到接收方中间要经过很多个`路由器`和`交换机`，攻击者可以在中途截获请求的数据。假设在一个网上存款系统中，一条消息表示用户支取了一笔存款，攻击者完全可以多次发送这条消息而偷窃存款。

重放是二次请求，如果API接口没有做对应的安全防护，将可能造成很严重的后果。  

API接口常见的安全防护要做的主要有如下几点：
* 防止sql注入
* 防止xss攻击
* 防止请求参数被串改
* 防止重放攻击

*** 

主要防御措施可以归纳为两点：
* 对请求的合法性进行校验
* 对请求的数据进行校验

>防止重放攻击必须要保证请求仅一次有效  
>需要通过在请求体中携带当前请求的唯一标识，并且进行签名防止被篡改。  
>所以防止重放攻击需要建立在防止签名被串改的基础之上。  

## 请求参数防篡改

采用https协议可以将传输的明文进行加密，但是黑客仍然可以截获传输的数据包，进一步伪造请求进行重放攻击。如果黑客使用特殊手段让请求方设备使用了伪造的证书进行通信，那么https加密的内容也将会被解密。
在API接口中我们除了使用https协议进行通信外，还需要有自己的一套加解密机制，对请求参数进行保护，防止被篡改。   
过程如下：

>1. 客户端使用约定好的秘钥对传输参数进行加密，得到签名值signature，并且将签名值也放入请求参数中，发送请求给服务端
>2. 服务端接收客户端的请求，然后使用约定好的秘钥对请求的参数（除了signature以外）再次进行签名，得到签名值autograph。
>3. 服务端对比signature和autograph的值，如果对比一致，认定为合法请求。如果对比不一致，说明参数被篡改，认定为非法请求。

因为黑客不知道签名的秘钥，所以即使截取到请求数据，对请求参数进行篡改，但是却无法对参数进行签名，无法得到修改后参数的签名值signature。   

签名的秘钥我们可以使用很多方案，可以采用对称加密或者非对称加密。


## 防止重放攻击

### 基于timestamp的方案  
每次HTTP请求，都需要加上timestamp参数，然后把timestamp和其他参数一起进行数字签名。因为一次正常的HTTP请求，从发出到达服务器一般都不会超过60s，所以服务器收到HTTP请求之后，首先判断时间戳参数与当前时间相比较，是否超过了60s，如果超过了则认为是非法的请求。  

一般情况下，黑客从抓包重放请求耗时远远超过了60s，所以此时请求中的timestamp参数已经失效了。
如果黑客修改timestamp参数为当前的时间戳，则signature参数对应的数字签名就会失效，因为黑客不知道签名秘钥，没有办法生成新的数字签名。  

但这种方式的漏洞也是显而易见的，如果在60s之后进行重放攻击，那就没办法了，所以这种方式不能保证请求仅一次有效。


### 基于nonce的方案
nonce的意思是仅一次有效的随机字符串，要求每次请求时，该参数要保证不同，所以该参数一般与时间戳有关，我们这里为了方便起见，直接使用时间戳的16进制，实际使用时可以加上客户端的ip地址，mac地址等信息做个哈希之后，作为nonce参数。  
我们将每次请求的nonce参数存储到一个“集合”中，可以json格式存储到数据库或缓存中。  
每次处理HTTP请求时，首先判断该请求的nonce参数是否在该“集合”中，如果存在则认为是非法请求。  

nonce参数在首次请求时，已经被存储到了服务器上的“集合”中，再次发送请求会被识别并拒绝。  
nonce参数作为数字签名的一部分，是无法篡改的，因为黑客不清楚token，所以不能生成新的sign。  

这种方式也有很大的问题，那就是存储nonce参数的“集合”会越来越大，验证nonce是否存在“集合”中的耗时会越来越长。我们不能让nonce“集合”无限大，所以需要定期清理该“集合”，但是一旦该“集合”被清理，我们就无法验证被清理了的nonce参数了。也就是说，假设该“集合”平均1天清理一次的话，我们抓取到的该url，虽然当时无法进行重放攻击，但是我们还是可以每隔一天进行一次重放攻击的。而且存储24小时内，所有请求的“nonce”参数，也是一笔不小的开销。

### 基于timestamp和nonce的方案
nonce的一次性可以解决timestamp参数60s的问题，timestamp可以解决nonce参数“集合”越来越大的问题。
防止重放攻击一般和防止请求参数被串改一起做，请求的Headers数据如下图所示。  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/designapi1.png" height="250" />

我们在timestamp方案的基础上，加上nonce参数，因为timstamp参数对于超过60s的请求，都认为非法请求，所以我们只需要存储60s的nonce参数的“集合”即可。
API接口验证流程：  

```java
// 获取token
String token = request.getHeader("token");
// 获取时间戳
String timestamp = request.getHeader("timestamp");
// 获取随机字符串
String nonceStr = request.getHeader("nonceStr");
// 获取请求地址
String url = request.getHeader("url");
// 获取签名
String signature = request.getHeader("signature");

// 判断参数是否为空
if (StringUtils.isBlank(token) || StringUtils.isBlank(timestamp) || StringUtils.isBlank(nonceStr) || StringUtils.isBlank(url) || StringUtils.isBlank(signature)) {
    //非法请求
    return;
}

//验证token有效性，得到用户信息
UserTokenInfo userTokenInfo = TokenUtils.getUserTokenInfo(token);

if (userTokenInfo == null) {
    //token认证失败（防止token伪造）
    return;
}

// 判断请求的url参数是否正确
if (!request.getRequestURI().equals(url)){
    //非法请求 (防止跨域攻击)
    return;
}

// 判断时间是否大于60秒
if(DateUtils.getSecond()-DateUtils.toSecond(timestamp)>60){
    //请求超时(防止重放攻击)
    return;
}

// 判断该用户的nonceStr参数是否已经在redis中
if (RedisUtils.haveNonceStr(userTokenInfo,nonceStr)){
    //请求仅一次有效（防止短时间内的重放攻击）
    return;
}

// 对请求头参数进行签名
String stringB = SignUtil.signature(token, timestamp, nonceStr, url,request);

// 如果签名验证不通过
if (!signature.equals(stringB)) {
    //非法请求（防止请求参数被篡改）
    return;
}

// 将本次用户请求的nonceStr参数存到redis中设置60秒后自动删除
RedisUtils.saveNonceStr(userTokenInfo,nonceStr,60);

//开始处理合法的请求
```

基于以上的方案就可以做到防止API接收的参数被篡改和防止API请求重放攻击。


文章转载自: https://www.lanshiqin.com/ef4382ec/  

