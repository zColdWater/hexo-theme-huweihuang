---
title: "[Backend] 第三方登录原理"
catalog: true
toc_nav_num: true
date: 2019-11-01 14:00:30
subtitle: "第三方登录原理"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Backend
catagories:
- Backend
---
 
# 第三方登录的原理 

所谓第三方登录，实质就是 OAuth 授权。用户想要登录 A 网站，A 网站让用户提供第三方网站的数据，证明自己的身份。获取第三方网站的身份数据，就需要 OAuth 授权

举例来说，A 网站允许 GitHub 登录，背后就是下面的流程。

A 网站让用户跳转到 GitHub。  
GitHub 要求用户登录，然后询问"A 网站要求获得 xx 权限，你是否同意？"  
用户同意，GitHub 就会重定向回 A 网站，同时发回一个授权码。  
A 网站使用授权码，向 GitHub 请求令牌。  
GitHub 返回令牌.  
A 网站使用令牌，向 GitHub 请求用户数据。  

在赋上一张序列图  
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/thirdlogin1.jpeg" height="250" />


# 实现流程

## 应用登记

一个应用要求 OAuth 授权，必须先到对方网站登记，让对方知道是谁在请求。

所以，你要先去 GitHub 登记一下。当然，我已经登记过了，你使用我的登记信息也可以，但为了完整走一遍流程，还是建议大家自己登记。这是免费的。

访问这个网址，填写登记表。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/thirdlogin2.png" height="250" />

应用的名称随便填，主页 URL 填写`http://localhost:8080`，跳转网址填写 `http://localhost:8080/oauth/redirect`。

提交表单以后，GitHub 应该会返回客户端 `ID（client ID）`和客户端密钥`（client secret）`，这就是应用的身份识别码。



## 客户端

创建一个本地的Web，http://localhost:8080，然后写一个按钮，跳转URL如下:  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/thirdlogin3.png" height="100" />

```
https://github.com/login/oauth/authorize?client_id=7e015d8ce32370079895&redirect_uri=http://localhost:8080/oauth/redirect
```


这个 URL 指向 GitHub 的 OAuth 授权网址，带有两个参数：`client_id`告诉 GitHub 谁在请求，`redirect_uri`是稍后跳转回来的网址。

用户点击到了 GitHub，GitHub 会要求用户登录，确保是本人在操作。


## 授权码

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/thirdlogin4.png" height="200" />

登录后，GitHub 询问用户，该应用正在请求数据，你是否同意授权。

用户同意授权， GitHub 就会跳转到`redirect_uri`指定的跳转网址，并且带上授权码，跳转回来的 `URL` 就是下面的样子。

```
http://localhost:8080/oauth/redirect?code=859310e7cecc9196f4af
```

后端收到这个请求以后，就拿到了授权码（`code`参数）。


## 后端实现

回调API是 `redirect_uri=http://localhost:8080/oauth/redirect`   

所以需要我们写一个Path是`/oauth/redirect`的API用来接Github第三方的回调拿到授权码Code. 


## 拿到Token去Github获取用户信息

拿授权码Code，ClientID，Secret 去Github服务器拿到Token，再通过Token去获取用户信息。

后端使用这个授权码，向 GitHub 请求令牌。

```js
const tokenResponse = await axios({
  method: 'post',
  url: 'https://github.com/login/oauth/access_token?' +
    `client_id=${clientID}&` +
    `client_secret=${clientSecret}&` +
    `code=${requestToken}`,
  headers: {
    accept: 'application/json'
  }
});
```

上面代码中，GitHub 的令牌接口`https://github.com/login/oauth/access_token`需要提供三个参数。

```
client_id：客户端的 ID
client_secret：客户端的密钥
code：授权码
```

作为回应，GitHub 会返回一段 JSON 数据，里面包含了令牌`accessToken`。

const accessToken = tokenResponse.data.access_token;



## API 数据

有了令牌以后，就可以向 API 请求数据了。

```js
const result = await axios({
  method: 'get',
  url: `https://api.github.com/user`,
  headers: {
    accept: 'application/json',
    Authorization: `token ${accessToken}`
  }
});
```

上面代码中，GitHub API 的地址是`https://api.github.com/user`，请求的时候必须在 HTTP 头信息里面带上令牌Authorization: token 361507da。

然后，就可以拿到用户数据，得到用户的身份。

```js
const name = result.data.name;
ctx.response.redirect(`/welcome.html?name=${name}`);
```