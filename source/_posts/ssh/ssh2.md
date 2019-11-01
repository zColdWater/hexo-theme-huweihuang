---
title: "[SSH] SSH免密登录和实现原理"
catalog: true
toc_nav_num: true
date: 2019-10-23 01:50:30
subtitle: "了解 SSH免密登录和实现原理"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- SSH
catagories:
- SSH
---


# SSH免密登录原理

[上一篇文章](https://zcoldwater.github.io/blog/article/ssh/ssh1/)，我们已经掌握如何使用ssh登录远程服务器了，但是每次登录都要输入密码，比较麻烦。ssh提供一种免密登录的方式：公钥登录。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/sshpublish11.jpg" height="350" />

1. 在客户端使用ssh-keygen生成一对密钥：公钥+私钥
2. 将客户端公钥追加到服务端的authorized_key文件中，完成公钥认证操作
3. 认证完成后，客户端向服务端发起登录请求，并传递公钥到服务端
4. 服务端检索authorized_key文件，确认该公钥是否存在
5. 如果存在该公钥，则生成随机数R，并用公钥来进行加密，生成公钥加密字符串pubKey(R)
6. 将公钥加密字符串传递给客户端
7. 客户端使用私钥解密公钥加密字符串，得到R
8. 服务端和客户端通信时会产生一个会话ID(sessionKey)，用MD5对R和SessionKey进行加密，生成摘要（即MD5加密字符串）
9. 客户端将生成的MD5加密字符串传给服务端
10. 服务端同样生成MD5(R,SessionKey)加密字符串
11. 如果客户端传来的加密字符串等于服务端自身生成的加密字符串，则认证成功
12. 此时不用输入密码，即完成建连，可以开始远程执行shell命令了


# SSH免密登录配置

原理介绍后，快让我们试一下免密登录吧。

1. 在本地客户端生成rsa密钥对 `ssh-keygen -t rsa` 不给密钥对设置密码，直接一路回车(Enter)。
2. 在本地客户端进入到 ~/.ssh 文件目录，然后找到 id_rsa.pub 文件，查看这个文件 `cat id_rsa.pub` 复制公钥。
3. 在Linux服务器进入到 ~/.ssh 文件目录，然后找到 authorized_keys 授权keys列表这个文件，然后编辑这个 authorized_keys 文件将 本地客户端 id_rsa.pub 内容拷贝进去，可以使用 `sudo vim authorized_keys` 编辑后 `:wq` 退出。
4. 在Linux服务器配置 /etc/ssh/sshd_config 文件，编辑 /etc/ssh/sshd_config 这个文件。

    ```
    RSAAuthentication yes
    PubkeyAuthentication yes

    # 另外，请留意 root 用户能否通过 SSH 登录：
    PermitRootLogin yes

    # 当你完成全部设置，并以密钥方式登录成功后，再禁用密码登录：
    PasswordAuthentication no
    ```
5. 最后重启SSH服务 `service sshd restart`


至此，本地客户端和Linux服务器上面的配置就OK了，接下来我们可以试试直接用SSH协议连接我们的服务器。

```
➜  ~ ssh ubuntu@xxx.xxx.xxx.xxx
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-54-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Oct 23 03:27:33 CST 2019

  System load:  0.0               Processes:           87
  Usage of /:   8.4% of 49.15GB   Users logged in:     0
  Memory usage: 27%               IP address for eth0: xxx.xx.x.xx
  Swap usage:   0%

 * Kata Containers are now fully integrated in Charmed Kubernetes 1.16!
   Yes, charms take the Krazy out of K8s Kata Kluster Konstruction.

     https://ubuntu.com/kubernetes/docs/release-notes

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

Last login: Wed Oct 23 01:28:00 2019 from xxx.xxx.xx.xx
```

直接登录无需密码，配置成功！ ✌️




参考:   
https://zhuanlan.zhihu.com/p/57876663  
https://blog.csdn.net/starter_____/article/details/79244410  


