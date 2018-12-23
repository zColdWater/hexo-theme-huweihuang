---
title: "[MAC] 如何查看和增加本地环境?"
catalog: true
toc_nav_num: true
date: 2018-12-23 09:50:30
subtitle: "MacOS 如何查看本地环境?"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/man_smoking.jpg"
tags:
- Basic
catagories:
- Basic
---

查询当前MacOS使用的Shell
=======
首先要知道你使用的MacOSX是什么样的Shell，使用命令
```bash
echo $SHELL
```
如果输出的是：`csh`或者是`tcsh`，那么你用的就是C Shell。  
如果输出的是：`bash`，`sh`，`zsh`，那么你的用的可能就是Bourne Shell的一个变种。  
Mac OS X 10.2之前默认的是C Shell。  
Mac OS X 10.3之后默认的是Bourne Shell。  


Mac配置环境变量的地方
=======

| 文件	        | 说明           |
| ------------- |:-------------:|
| /etc/paths| （全局建议修改这个文件） |
| /etc/profile | （建议不修改这个文件）全局（公有）配置，不管是哪个用户，登录时都会读取该文件。 |
| /etc/bashrc | （一般在这个文件中添加系统级环境变量）全局（公有）配置，bash shell执行时，不管是何种方式，都会读取此文件。 |
| ~/.profile | 文件为系统的每个用户设置环境信息,当用户第一次登录时该文件被执行，并从/etc/profile.d目录的配置文件中搜集shell的设置。使用注意：如果你有对/etc/profile有修改的话必须得重启你的修改才会生效，此修改对每个用户都生效。 |
| ~/.bash_profile | 该文件包含专用于你的bash shell的bash信息，当登录时以及每次打开新的shell时，该文件被读取.（每个用户都有一个.bashrc文件，在用户目录下）使用注意：需要需要重启才会生效，/etc/profile对所有用户生效，~/.bash_profile只对当前用户生效。 |
| ~/.bashrc	 | 每一个运行bash shell的用户执行此文件。当bash shell被打开时,该文件被读取。使用注意：对所有的使用bash的用户修改某个配置并在以后打开的bash都生效的话可以修改这个文件，修改这个文件不用重启，重新打开一个bash即可生效。 |

MAC OS X环境配置的加载顺序

 ```bash
 # 系统级别
/etc/profile
/etc/paths 

# 用户级别
~/.bash_profile 
~/.bash_login 
~/.profile 

~/.bashrc
 ```
~/.bash_profile，~/.bash_login，~/.profile依次加载，如果~/.bash_profile不存在，依次加载后面几个文件；如果~/.bash_profile文件存在，后面几个文件不会加载  
~/.bashrc 是bash shell打开时候加载


查看当前用户环境变量
=======
```bash
echo $PATH
```

增加环境变量
=======

```bash
# 使用冒号隔开
export PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:------:<PATH N>
# 或者 
export PATH=${PATH}:<PATH 1>
export PATH=${PATH}:<PATH 2>

# 第一种将路径合并在一起，不方便删除，建议使用第二种，换行挨个设置
```

例如: 下面使用的就是分开的写法
```bash
# export AAA
export AAA=/Users/YourUserName/Documents/Dev/hadoop-2.7.3
export PATH=$PATH:$AAA/bin:$AAA/sbin

# export BBB
export BBB=/Applications/Splunk
export PATH=$PATH:$BBB/bin

```

使当前环境立马生效的命令
=======
```bash
# 使当前用户的环境变量立刻生效。 如果你的电脑是 Bourne Shell 的话。
source .bash_profile
```