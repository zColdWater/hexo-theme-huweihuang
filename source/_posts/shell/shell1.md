---
title: "[SHELL] SHELL小知识"
catalog: true
toc_nav_num: true
date: 2019-06-30 14:00:30
subtitle: "记录一些SHELL的小命令和小知识"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/girl-min.png"
tags:
- SHELL
catagories:
- SHELL
---

> 本文只是记录一些基本的命令和知识来处理SHELL。

前言
=======
mac 一般使用bash作为默认shell  
在 Linux 里执行这个命令和 Mac 略有不同，你会发现 Mac 多了一个 zsh，也就是说 OS X 系统预装了个 zsh，这是个神马 Shell 呢？  
目前常用的 Linux 系统和 OS X 系统的默认 Shell 都是 bash，但是真正强大的 Shell 是深藏不露的 zsh， 这货绝对是马车中的跑车，跑车中的飞行车，史称『终极 Shell』，但是由于配置过于复杂，所以初期无人问津，很多人跑过来看看 zsh 的配置指南，什么都不说转身就走了。直到有一天，国外有个穷极无聊的程序员开发出了一个能够让你快速上手的zsh项目，叫做「oh my zsh」，[Github](https://github.com/robbyrussell/oh-my-zsh)。

BASH中读取环境变量
=======

Mac系统的环境变量，加载顺序为：

`/etc/profile` 系统级别的(系统加载就会加载,修改需要Root权限)  
`/etc/paths`  系统级别的(系统加载就会加载,修改需要Root权限)  
`~/.bash_profile`  /.bash_profile文件存在，则后面的几个文件就会被忽略不读了，如果/.bash_profile文件不存在，才会以此类推读取后面的文件 单个用户设置  
`~/.bash_login`  
`~/.profile`  
`~/.bashrc`

设置完PATH后，记得需要使用 `source ~/.bash_profile` 来使立即生效。 这里只是用 .bash_profile 作为一个例子，具体你修改了哪个文件，就source后跟哪个文件就可以。

ZSH中读取环境变量
=======

`~/.zshrc` 

常用命令
=======
1. ps -p $$: 查看当前终端运行的是哪种shell
2. 设置PATH **中间用冒号隔开** `export PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:------:<PATH N>`


简单Demo
=======
> 我有个需求，就是执行一个shell将我当前的改动 全部上传到 master，并且 message 是 "update"，要么我每次都需做一遍 `git add .` `git commit -m"update"` `git push origin -u master`，那么有没有可能，我输入一行命令，用脚本帮我去跑剩下的命令呢。

第一步: 编写shell脚本 名字叫做 auto_push.sh
 ```
    #!/bin/bash
    echo "==Start=="
    cd /Users/yongpeng.zhu/Desktop/Blog/myblog
    git add .
    git commit -m"update"
    git push origin master
    echo "==End=="
 ```

第二步: 添加到本地环境变量里 vi ~/.bash_profile 在这个文件
```
    // 备注: /Users/yongpeng.zhu/Desktop/Blog/myblog 是 auto_push.sh 的根目录
    export PATH="$PATH:/Users/yongpeng.zhu/Desktop/Blog/myblog"
```

第三步: 使.bash_profile文件立即生效 执行 `source ~/.bash_profile`

第四步: 在任意目录下执行 `auto_push.sh` 看看效果.


遇到的问题
=======

1. Command not found
    ```
    ➜  ~ sudo /Users/yongpeng.zhu/Desktop/Blog/myblog/auto_push.sh
    Password:
    sudo: /Users/yongpeng.zhu/Desktop/Blog/myblog/auto_push.sh: command not found
    ```
    解决办法: ` chmod +x /Users/yongpeng.zhu/Desktop/Blog/myblog/auto_push.sh` 授予权限，再执行shell脚本即可。
  
2. 这个问题的源头是这样的，我的终端是 iTerm2，安装了 zsh 和 oh-my-zsh，自此，我打开命令行的时候不在执行 ~/.bash_profile，如果要想让这个文件有效，那么修改 zsh 的配置文件： 编辑 ~/.zshrc，在文件里加入一行： `source ~/.bash_profile`




