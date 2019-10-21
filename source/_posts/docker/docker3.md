---
title: "[Docker] Docker 基础使用<二>  "
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


