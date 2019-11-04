---
title: "[VPN] 利用VPS搭建翻墙站点"
catalog: true
toc_nav_num: true
date: 2019-06-04 21:00:30
subtitle: "如何利用VPS搭建可以翻墙的站点呢？"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- VPN
catagories:
- VPN
---


VPS是什么
===============
VPS 全称 **Virtual private server，虚拟专用服务器**，是将一台服务器分区成多个虚拟专享服务器的服务。每个VPS可配置独立IP、内存、CPU资源、操作系统。


VPS与ECS区别
===============
云服务器（Elastic Compute Service，简称 ECS），例如 阿里云ECS，是一种简单高效、处理能力可弹性伸缩的计算服务。 简单来说就是能在一组服务器资源（CPU、内存等）调度。而VPS只能在一台服务器分配资源。

ECS灵活性、高可用性比VPS高，但价格也比VPS高。好像阿里云ECS，最低配CPU单核、内存1G、储存40G也要80元/月（还有带宽的账要算）；而国外很多VPS，标准配置CPU单核、内存1G、储存20G，只要5美元/月（目前34.416人民币/月），而且按时收费。（本文介绍的最低配VPS只要2.5美元/月）

VPS能做什么
===============
如果你是开发者，需要搭建个人网站，或者跑一些demo，VPS足够了。当然，你个人预算充足对服务器性能有要求，那就ECS吧。如果跑公司的业务，千万不要用VPS，VPS非高可用，万一挂了给公司带来很大的损失。

我们还能用VPS搭建ShadowSocks做翻墙！一个人独占一条线路！稳定性高！

VPS提供商——Vultr.com
===============
国外的VPS提供商有好多，有名的[Vultr](https://www.vultr.com/)、[Digital Ocean](https://www.digitalocean.com/?refcode=ad240a3367a0&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=CopyPaste)、[Linode](https://www.linode.com/)、[搬瓦工（bandwagonhost）](https://bwh1.net/)。本文仅介绍Vultr，因为2.5美元/月性价比最高。

注册购买VPS流程
===============

连接: https://www.vultr.com/ 


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps1.png" height="200" />


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps2.png" height="200" />


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps3.png" height="200" />


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps4.png" height="200" />


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps5.png" height="200" />


<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps6.png" height="200" />


登陆购买的VPS服务
===============
这里介绍的MacOS下登陆购买的VPS服务器的流程。 

这里需要注意几个地方，如果在中国大陆，需要对购买的服务器ip进行`ping`操作，查看是否可以 连接，因为大陆会封一些IP。

1. 执行 `ping xx.xx.xx.xx(根据自己申请的VPS的地址来进行测试)`
2. 如果`ping`后没有出现`timeout`警告，证明可以访问。
3. 使用`ssh`进行登陆，`ssh root@xx.xx.xx.xx`，这里顺带说一句，最好在购买服务器的控制面板里找到配置`ssh key`的地方，然后将本地的`ssh pubKey`配置进去，就跟配置git一样.


登陆进来之后:

运行以下命令:  
```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

选择脚本（Python、R、Go、libev），任选一个:  
```
Which Shadowsocks server you'd select:
1.Shadowsocks-Python
2.ShadowsocksR
3.Shadowsocks-Go
4.Shadowsocks-libev
Please enter a number (default 1):
```

笔者选择Shadowsocks-Go，输入3......然后，输入密码和端口，笔者直接回车用默认:

```
You choose = Shadowsocks-Go

Please enter password for Shadowsocks-Go
(default password: teddysun.com):

password = teddysun.com

Please enter a port for Shadowsocks-Go [1-65535]
(default port: 8989):

port = 8989


Press any key to start...or Press Ctrl+C to cancel
```

安装成功后，命令行出现(下面信息已经做过修改是访问不通的):

```
Congratulations, Shadowsocks-Go server install completed!
Your Server IP        :  12.79.118.192
Your Server Port      :  8989
Your Password         :  teddysun.com
Your Encryption Method:  aes-256-cfb

Your QR Code: (For Shadowsocks Windows, OSX, Android and iOS clients)
 ss://AUVlLUR1PP1jZlI6dIVkZflzda4uY29tQDA1Ljc2LjE4OC4xOTI6ODk4OQ==
Your QR Code has been saved as a PNG file path:
 /root/shadowsocks_go_qr.png

Welcome to visit: https://teddysun.com/486.html
Enjoy it!
```

Mac配置客户端的Shadowsocks
===============

1. Shadowsocks Mac 客户端下载地址: https://github.com/shadowsocks/ShadowsocksX-NG/releases 下载最新的ShadowsocksX-NG.app.zip，然后安装在Mac中


2. 配置客户端:

    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/vps7.png" height="200" />

3. 选择开启 自动模式/全局模式 然后登陆 Google / Youtube 试试喽。


加速 TCP BBR 拥塞控制算法
===============
TCP BBR 是 Google 开源的 拥塞控制算法，类似锐速的单边加速工具。由于受到各方面限制，国外的vps速度不理想，偶尔有延迟、不稳定的现象出现。而bbr的作用，就是要解决这一问题。

我们只需要在vps上安装即可，参考[《一键安装最新内核并开启 BBR 脚本》](https://teddysun.com/489.html)。

使用root用户登录，运行以下命令：

```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
chmod +x bbr.sh
./bbr.sh
```

安装完成后，脚本会提示需要重启 VPS，输入 y 并回车后重启。重启后，执行命令：

```
lsmod | grep bbr
```

返回值有 tcp_bbr 模块即说明bbr已启动。

（TCP BBR要求Linux内核4.10以上，如果安装提示内核版本太低，去[《一键安装最新内核并开启 BBR 脚本》](https://teddysun.com/489.html)查看升级内核方法）

BBR成功安装后，shadowsocks速度有明显提升。


小结
===============
笔者希望通过本文，让读者对vps有初步认识，并轻松搭建shadowsocks做代理服务器，体验国外的世界。

工信部已经明文规定，国内不允许有VPN之类的产品，所以以后想购买国内的VPN服务是不可能了，即使碰到有，说不定某天就被天朝封了。笔者认为天朝也不会赶尽杀绝，毕竟不上外国网站，对开发来说太痛苦，不利于科技发展。


相关连接: https://www.vpsdx.com/912.html VPS大学
