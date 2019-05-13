---
title: "[Spring] 开始一个新的SpringBoot项目 Mac"
catalog: true
toc_nav_num: true
date: 2019-05-13 10:51:30
subtitle: "如何开始一个新的SpringBoot项目"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- Spring
catagories:
- Spring
---

Demo: xxxxxxx

> 当前环境: java 12.0.1 2019-04-16 Java(TM) SE Runtime Environment (build 12.0.1+12) 备注: Mac

简述
=======
这篇文章主要来演示如何创建一个新的SpringBoot项目,我们用到的工具有: 

1. [VSCode](https://code.visualstudio.com/) 点击跳转官网自行下载  
2. [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html) 配置Java环境
3. [MySQL](https://gist.github.com/nrollr/3f57fc15ded7dddddcc4e82fe137b58e) 数据库 点击查看Mac如何安装
4. [MySQL图形化软件Querious](https://www.waitsun.com/querious-2-1-12.html) 由于这个软件是收费的，我又没钱，这里我下载的是破解版的。当然大家尽力支持正版。 

Step1
=======
打开`VSCode`，安装扩展。 
1. 需要的扩展:
   1. `Atom One Dark Theme` (好看些？)
   2. `Chinese (Simplified) Language Pack for Visual Studio Code `(VSCode汉化包)
   3. `XML` (支持XML格式)
   4. `Spring Initializr Java Support` (快捷创建Spring项目)
   5. `Spring Boot Tools` (SpringBoot框架高亮 智能补齐等)
   

2. `Cmd`+`Shift`+`P` (安装上面的拓展之后记得重启一下VSCode激活):
   1. 输入 `spring maven project`，选择这个选项卡。
   2. 接着选择语言 `Java`
   3. 输入GroupID `com.example`
   4. 输入Artifact `demo`
   5. 指定SpringBoot版本 `2.1.4`
   6. 选择项目模版 `web` 输入Enter回车



Step2
=======
使用`VSCode`打开我们刚才创建的`demo`项目。  

1. 添加必要的依赖和相关代码:
   1. 打开根目录下面的`pom.xml`文件,设置依赖在 `<dependencies>` 属性下面添加: 
      * 代码如下 分别 `ORM` , `数据库`:
            ```XML
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
            </dependency>

            <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <!-- <version>5.1.6</version> -->
            </dependency>
            ```
    2. 添加连接数据库的代码:
       * 在根目录`src`->`main`->`resource`->`application.propertes`添加: 
          ```java
            spring.datasource.url=jdbc:mysql://localhost:3306/nio_cube  (nio_cube是你创建的数据库名称)
            spring.datasource.username=root (数据库用户名)
            spring.datasource.password=123456 (数据库密码)
            spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver（指明jdbc驱动）
          ```

    3. 编写业务代码: 
       * 这里我就不再讨论了，相关代码请看文章开头，Demo链接。 自行下载运行即可

2. 配置数据库: 
   1. 下载Mysql，然后设置密码，然后验证MySQL是否可以正常启动。这里有一份[`MySQL`的命令文档](https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-shell-commands.html)
   2. 配置图形化软件连接数据库 这里不再谈论了，非常简单。按照软件提示 输入相关信息就可以。


Step3
=======
启动项目:

1. 为`Demo`项目添加`Launch.json`，在VSCode的调试模式下，`Cmd`+`Shift`+`D`，点击运行按钮右边的`小齿轮图标`，选择·Java·项目，然后点击 `运行图标`。 注意按下面的输出，没有异常报错就是启动成功了。

2. 验证你的项目，找到你写的`api`，在浏览器中调用一下，或者`postman`调用一下，就知道结果了。

我遇到的问题
=======
1. 彻底清空环境里的`MySQL` 
   ```
    brew remove mysql
    brew cleanup
    launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
    rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
    sudo rm -rf /usr/local/var/mysql

    brew install mysql
    mysql.server start # no sudo!
   ``` 
2. 数据库图表联系: https://dbdiagram.io/home 工具挺好用
3. MySQL 最基础的命令: 
   1. `mysql -uroot -p1234` (连接数据库 root用户 1234密码)
   2. `mysql.server status` (查看MySql数据库状态，运行还是关闭等)
   3. `mysql.server start` (启动数据库)
   4. `mysql.server start` (关闭数据库)

总结
=======
因为我是前端，平时很少写服务端项目，接触的也是`Node`这种，如果有不正确的地方希望指出。  
整体的步骤如上，其中有些地方我做了省略，因为`Demo`项目中有，我就不在这里贴上了。



