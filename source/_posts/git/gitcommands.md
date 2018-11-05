---
title: "Git Commands"
catalog: true
toc_nav_num: true
date: 2018-11-05 01:00:30
subtitle: "常用的git命令"
header-img: "https://upload-images.jianshu.io/upload_images/1793544-8272181ec2013baa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"
tags:
- Git
catagories:
- Git
---

Git-三种状态
=======

> 已暂存（staged），已提交（committed）和 已修改（modified）

  * 已暂存（staged） 通俗地说，git会把被git管理的文件的快照放入暂存区域，比如: `git add .` 这步骤其实就是将所有文件添加到暂存区，经过这步骤所有的改动就添加到暂存区了。

  * 已提交（committed）这个状态是通过 提交更新，找到暂存区域的文件，将快照永久性存储到 git 仓库目录 达到的。 例如 `git commit` 这其实就是将暂存区的文件状态 改变成已提交，接下来就可以使用`git push`来上传代码到远端了。

  * 已修改（modified）任何添加git管理的文件发生了变化都会改变文件状态，比如: 修改一个文件内容，那么文件状态如果之前不是`modified`状态 就会变成这个状态。


初次运行 Git 前的配置
=======

> 既然已经在系统上安装了 Git，你会想要做几件事来定制你的 Git 环境。 每台计算机上只需要配置一次，程序升级时会保留配置信息。 你可以在任何时候再次通过运行命令来修改它们。

> Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

> /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的 git config 时，它会从此文件读写配置变量。

> ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 可以传递 --global 选项让 Git 读写此文件。

> 当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：针对该仓库。

> 每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。

* 用户信息:
    * 当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。 这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：
    * `$ git config --global user.name "John Doe"`
    * `$ git config --global user.email johndoe@example.com`
    * 再次强调，如果使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置。

* 文本编辑器:
    * 既然用户信息已经设置完毕，你可以配置默认文本编辑器了，当 Git 需要你输入信息时会调用它。 如果未配置，Git 会使用操作系统默认的文本编辑器，通常是 Vim。 如果你想使用不同的文本编辑器，例如 Emacs，可以这样做：`$ git config --global core.editor emacs`

* 检查配置信息:
    * 如果想要检查你的配置，可以使用 git config --list 命令来列出所有 Git 当时能找到的配置。
    
    ```
        $ git config --list
        user.name=John Doe
        user.email=johndoe@example.com
        color.status=auto
        color.branch=auto
        color.interactive=auto
        color.diff=auto
    ```
    * 你可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置（例如：`/etc/gitconfig` 与 `~/.gitconfig`）。 这种情况下，Git 会使用它找到的每一个变量的最后一个配置。
    
    * 你可以通过输入 git config <key>： 来检查 Git 的某一项配置

    ```
    $ git config user.name
    John Doe
    ```


---