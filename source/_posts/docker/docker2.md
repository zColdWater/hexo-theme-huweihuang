---
title: "[Docker] Docker基础使用一 "
catalog: true
toc_nav_num: true
date: 2019-10-21 01:00:30
subtitle: "一起了解和掌握使用Docker"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
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


# 镜像的制作

装完Docker后，就可以操作镜像了(`Image`)。

## 搜索下载查看镜像

`$ docker search centos`    # 查看centos镜像是否存在

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

`$ docker pull centos`    # 利用pull命令获取镜像

```
➜  ~ docker pull centos
Using default tag: latest
latest: Pulling from library/centos
729ec3a6ada3: Pull complete
Digest: sha256:f94c1d992c193b3dc09e297ffd54d8a4f1dc946c37cbeceb26d35ce1647f88d9
Status: Downloaded newer image for centos:latest
```

`$ docker images`    # 查看当前系统中的images信息
```
➜  ~ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              0f3e07c0138f        2 weeks ago         220MB
jenkins/jenkins     latest              08b7b3e99b5a        4 months ago        566MB
jenkins             latest              cd14cecfdb3a        15 months ago       696MB
```

以上是下载一个已有镜像，此外有两种方法可以帮助你新建自有镜像。   


## 制作自己的镜像

1.利用已有镜像然后修改提交更改称为自己的镜像    

`$ docker run -it centos:latest /bin/bash` # 启动一个容器
```
➜  ~ docker run -it centos:latest /bin/bash
[root@a7665b026a60 /]# 
```

`$ yum install git` # 利用yum安装git  
`$ git --version` # 此时的容器中已经装有git了
```
[root@89937c8d3a30 /]# git --version
git version 2.18.1
```

此时利用`exit`退出该容器，然后查看docker中运行的程序容器。  

```
➜  ~ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
89937c8d3a30        centos:latest       "/bin/bash"              10 minutes ago      Exited (127) 2 seconds ago                        dreamy_golick
a7665b026a60        centos:latest       "/bin/bash"              11 minutes ago      Exited (127) 11 minutes ago                       naughty_mccarthy
392ce9134f0e        jenkins/jenkins     "/sbin/tini -- /usr/…"   4 months ago        Created
```

这里将容器转化为一个镜像，即执行`commit`操作，完成后可使用`docker images`查看:  
`$ docker commit -m "centos install git" -a "henry" 89937c8d3a30 henry/dreamy_golick:git`  

```
➜  ~ docker commit -m "centos install git" -a "henry" 89937c8d3a30 henry/dreamy_golick:git
sha256:d68d34e1e7a48070dcdcdd3984b1157dec85ba80c1da21a2bddf970183e9723d
```

其中，-m指定说明信息；-a指定用户信息；89937c8d3a30代表容器的id；henry/dreamy_golick:git指定目标镜像的用户名、仓库名和 tag 信息。注意这里的用户名henry，后边会用到。


`$ docker images` 查看现在的镜像，可以看到TAG是git，CREATED是5 minutes。  

```  

REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
henry/dreamy_golick   git                 d68d34e1e7a4        5 minutes ago       346MB
centos                latest              0f3e07c0138f        2 weeks ago         220MB
jenkins/jenkins       latest              08b7b3e99b5a        4 months ago        566MB
jenkins               latest              cd14cecfdb3a        15 months ago       696MB

```  

`$ docker run -it henry/dreamy_golick:git /bin/bash` 进入刚提交的镜像

```
➜  ~ docker run -it henry/dreamy_golick:git /bin/bash
```

`$ docker ps -a` 列出所有容器，可以看出IMAGE是`henry/dreamy_golick:git`就是我们更新的容器。  

```
➜  ~ docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED              STATUS                        PORTS               NAMES
8c09a960ec5e        henry/dreamy_golick:git   "/bin/bash"              About a minute ago   Exited (0) 2 seconds ago                          naughty_nobel
89937c8d3a30        centos:latest             "/bin/bash"              34 minutes ago       Exited (127) 24 minutes ago                       dreamy_golick
a7665b026a60        centos:latest             "/bin/bash"              35 minutes ago       Exited (127) 35 minutes ago                       naughty_mccarthy
392ce9134f0e        jenkins/jenkins           "/sbin/tini -- /usr/…"   4 months ago         Created                                           jenkins
```


2.利用Dockerfile创建镜像    

`Dockerfile`可以理解为一种配置文件，用来告诉`docker build`命令应该执行哪些操作，一个简易的`Dockerfile`文件如下所示 [官方传送门](https://docs.docker.com/engine/reference/builder/)  

```  

# 说明该镜像以哪个镜像为基础
FROM centos:latest

# 构建者的基本信息
MAINTAINER xianhu

# 在build这个镜像时执行的操作
RUN yum update
RUN yum install -y git

# 拷贝本地文件到镜像中
COPY ./* /usr/share/gitdir/  

```


有了`Dockerfile`之后，就可以利用`build`命令构建镜像了  

`$ docker build -t="henry/dreamy_golick:git1"`   

其中`-t`用来指定新镜像的用户信息、`tag`等。最后的点表示在当前目录寻找`Dockerfile`   

构建完成之后，同样可以使用`docker images`命令查看:  




# 镜像删除 容器删除

如果想删除容器或者镜像，可以使用rm命令

```
$ docker rm container_name/container_id
$ docker rmi image_name/image_id # 删除镜像前 需要把此镜像生成的容器全部删除。
```



# 总结

这篇文章 先介绍这么多，下篇继续介绍一些 Docker的 其他基础用法。




