---
title: "[Docker] Docker基础使用二"
catalog: true
toc_nav_num: true
date: 2019-10-21 15:00:30
subtitle: "一起了解和掌握使用Docker"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Docker
catagories:
- Docker
---

上一篇文章介绍了创建自己的镜像，这篇文章介绍下容器的基础操作。

# 容器的基础操作

在前边镜像的章节中，我们已经看到了如何基于镜像启动一个容器，即docker run操作。

```
$ docker run -it centos:latest /bin/bash
```

这里-it是两个参数：-i和-t。前者表示打开并保持stdout，后者表示分配一个终端（pseudo-tty）。此时如果使用exit退出，则容器的状态处于Exit，而不是后台运行。如果想让容器一直运行，而不是停止，可以使用快捷键 ctrl+p ctrl+q 退出，此时容器的状态为Up。

除了这两个参数之外，run命令还有很多其他参数。其中比较有用的是-d后台运行：

```
$ docker run centos:latest /bin/bash -c "while true; do echo hello; sleep 1; done"
$ docker run -d centos:latest /bin/bash -c "while true; do echo hello; sleep 1; done"
```

这里第二条命令使用了`-d`参数，使这个容器处于后台运行的状态，不会对当前终端产生任何输出，所有的`stdout`都输出到`log`，可以使用`docker logs container_name/container_id`查看。  

启动、停止、重启容器命令：

```
$ docker start container_name/container_id
$ docker stop container_name/container_id
$ docker restart container_name/container_id
```  

后台启动一个容器后，如果想进入到这个容器，可以使用`attach`命令   

```
$ docker attach container_name/container_id #进入后台运行的容器
```

删除容器的命令前边已经提到过了:

```
$ docker rm container_name/container_id
```



# 仓库的基础操作

Docker官方维护了一个DockerHub的公共仓库，里边包含有很多平时用的较多的镜像。除了从上边下载镜像之外，我们也可以将自己自定义的镜像发布（push）到DockerHub上。

在镜像操作章节中，我们新建了一个xianhu/centos:git镜像。

（1）访问https://hub.docker.com/，如果没有账号，需要先注册一个。

（2）利用命令docker login登录DockerHub，输入用户名、密码即可登录成功：

```
[root@xxx ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xianhu
Password:
Login Succeeded
```

（3）将本地的镜像推送到DockerHub上，这里的xianhu要和登录时的username一致：

```
[root@xxx ~]# docker push xianhu/centos:git    # 成功推送
[root@xxx ~]# docker push xxx/centos:git    # 失败
The push refers to a repository [docker.io/xxx/centos]
unauthorized: authentication required
```

（4）以后别人就可以从你的仓库中下载合适的镜像了。

```
[root@xxx ~]# docker pull xianhu/centos:git
```

对应于镜像的两种创建方法，镜像的更新也有两种：

* 创建容器之后做更改，之后commit生成镜像，然后push到仓库中。  
* 更新Dockerfile。在工作时一般建议这种方式，更简洁明了。

这里再一次回顾一下三个重要的概念：镜像、容器、仓库：

> 从仓库（一般为DockerHub）下载（pull）一个镜像，Docker执行run方法得到一个容器，用户在容器里执行各种操作。Docker执行commit方法将一个容器转化为镜像。Docker利用login、push等命令将本地镜像推送（push）到仓库。其他机器或服务器上就可以使用该镜像去生成容器，进而运行相应的应用程序了。



