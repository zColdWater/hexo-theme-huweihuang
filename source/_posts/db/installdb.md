---
title: "[DB] CentOS7下用yum安装Mysql指定版本"
catalog: true
toc_nav_num: true
date: 2019-06-11 20:18:30
subtitle: "CentOS7下用yum安装Mysql"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/camper.jpg"
tags:
- DB
catagories:
- DB
---

> 当前版本: mysql  Ver 14.14 Distrib 5.7.28, for Linux (x86_64) using  EditLine wrapper

# 安装Mysql  

先卸载MariaDB数据库:   
由于Centos7默认会安装MariaDB、我们需要Mysql、所以需要先卸载MariaDB。   

查询一下MariaDB是否安装:  
yum list installed | grep mariadb

全部卸载MariaDB:  
yum -y remove mariadb*


## 下载Mysql5.7 最新版本

从Mysql官网下载Mysql源  
`wget -P /home/tools http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm`


安装mysql源
`sudo wget -P /home/tools http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm`


添加配置  
`vim /etc/yum.repos.d/mysql-community.repo`

```
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

然后就可以安装 mysql5.7版本啦    
`yum install mysql-community-server -y`

## 安装完成

如果看到下面这样的信息、就说明安装成功啦、进入下一步；

```
已安装:
  mysql-community-server.x86_64 0:5.7.25-1.el7                                            

作为依赖被安装:
  mysql-community-client.x86_64 0:5.7.25-1.el7
  mysql-community-common.x86_64 0:5.7.25-1.el7
  mysql-community-libs.x86_64 0:5.7.25-1.el7

完毕！
```

## 配置一下启动MYSQL

启动MYSQL  
`systemctl start mysqld.service`

查看状态  
`systemctl status mysqld.service`

添加开机启动  
`systemctl enabled mysqld.service`


## MYSQL 修改root用户密码  

首先查看默认密码   
`grep 'password' /var/log/mysqld.log`  

拿到默认密码之后登录mysql   
`mysql -u root -p` 


修改密码  
`ALTER USER 'root'@'localhost' IDENTIFIED BY 'Abcd#123';`


## 开启远程访问 
前面的步骤完成之后我们想在windows电脑上用Navicat来连接的时候、发现是连不上的；

一般来说你的帐号不允许从远程登陆，这个时候只要在localhost的那台服务器，登入mysql，
更改 mysql 数据库里的 user 表里的 host 项，从localhost改称%就能远程访问了！

```
[root@localhost ~]# mysql -u root -p 
mysql>use mysql; 
mysql>update user set host = '%' where user = 'root'; 
mysql>select host, user from user;
mysql>flush privileges;
```

到此 可以用你的Navicat等工具来远程连接MYSQL服务器啦


# 注意事项 
1. 如果提示获取 GPG 密钥失败; 请修改gpgcheck=0即可、也就是不检查了 
2. MYSQL的repo网站上可以下载你需要的所有版本的源、请自行选择即可 
3. 如果打开mysql-community.repo的文件默认有内容、那么就直接修改、将你需要安装的版本下的enabled=1即可。


# 参考文章  
http://hiadmin.org/2019/04/02/CentOS7-Yum-Install-Mysql/  
