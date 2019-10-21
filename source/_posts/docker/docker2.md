---
title: "[Docker] Docker 基础使用 "
catalog: true
toc_nav_num: true
date: 2019-10-21 01:00:30
subtitle: "一起了解和掌握使用Docker"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Docker
catagories:
- Docker
---

这篇文章介绍Docker的基础使用，看过上一篇文章的小伙伴，应该知道在Docker中，仓库，镜像，容器，他们的关系。 如果不知道这里有  [传送门](https://zcoldwater.github.io/blog/article/docker/docker1/)


# 注册服务器Mirror加速 (遇到问题的童鞋可以试下)

注意: 国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。国内很多云服务商都提供了国内加速器服务，例如：  
Azure中国镜像 https://dockerhub.azk8s.cn   
阿里云加速器(需登录账号获取)  
七牛云加速器 https://reg-mirror.qiniu.com   

> 由于镜像服务可能出现宕机，建议同时配置多个镜像。  
>国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务，具体请参考官方文档。

当前系统: Ubuntu 16.04+、Debian 8+、CentOS 7  

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）   

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```

> 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。   

之后重新启动服务。

```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```


# 镜像的基础操作

装完Docker后，就可以操作镜像了(`Image`)。

`$docker search centos`    # 查看centos镜像是否存在

```
➜  ~ docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   5617                [OK]
ansible/centos7-ansible            Ansible on Centos7                              124                                     [OK]
jdeathe/centos-ssh                 OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   114                                     [OK]
consol/centos-xfce-vnc             Centos container with "headless" VNC session…   99                                      [OK]
centos/mysql-57-centos7            MySQL 5.7 SQL database server                   63
imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              57                                      [OK]
tutum/centos                       Simple CentOS docker image with SSH access      44
centos/postgresql-96-centos7       PostgreSQL is an advanced Object-Relational …   39
kinogmt/centos-ssh                 CentOS with SSH                                 29                                      [OK]
pivotaldata/centos-gpdb-dev        CentOS image for GPDB development. Tag names…   10
guyton/centos6                     From official centos6 container with full up…   9                                       [OK]
drecom/centos-ruby                 centos ruby                                     6                                       [OK]
centos/tools                       Docker image that has systems administration…   4                                       [OK]
darksheer/centos                   Base Centos Image -- Updated hourly             3                                       [OK]
mamohr/centos-java                 Oracle Java 8 Docker image based on Centos 7    3                                       [OK]
pivotaldata/centos                 Base centos, freshened up a little with a Do…   3
pivotaldata/centos-mingw           Using the mingw toolchain to cross-compile t…   2
miko2u/centos6                     CentOS6 日本語環境                                   2                                       [OK]
pivotaldata/centos-gcc-toolchain   CentOS with a toolchain, but unaffiliated wi…   2
indigo/centos-maven                Vanilla CentOS 7 with Oracle Java Developmen…   1                                       [OK]
blacklabelops/centos               CentOS Base Image! Built and Updates Daily!     1                                       [OK]
mcnaughton/centos-base             centos base image                               1                                       [OK]
pivotaldata/centos7-dev            CentosOS 7 image for GPDB development           0
smartentry/centos                  centos with smartentry                          0                                       [OK]
pivotaldata/centos6.8-dev          CentosOS 6.8 image for GPDB development         0
```

