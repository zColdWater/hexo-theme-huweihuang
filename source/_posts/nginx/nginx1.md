---
title: "[Nginx] Nginx小知识"
catalog: true
toc_nav_num: true
date: 2019-06-28 19:00:30
subtitle: "这里介绍一些Nginx的小知识，可能比较零散，因为笔者也是新手，之所以比较零散也记的原因也是以此做笔记，日后再整理。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/girl-min.png"
tags:
- Nginx
catagories:
- Nginx
---

本人也是小白，这里记录一些 Nginx 常用的小知识。

前言: 介绍一些常用的Nginx命令

常用小知识:
=======

1. Nginx如何做到热部署? 
    ```
    所谓热部署，就是配置文件nginx.conf修改后，不需要stop Nginx，不需要中断请求，就能让配置文件生效！（nginx -s reload 重新加载/nginx -t检查配置/nginx -s stop）
    通过上文我们已经知道worker进程负责处理具体的请求，那么如果想达到热部署的效果，可以想象：

    方案一：
    修改配置文件nginx.conf后，主进程master负责推送给woker进程更新配置信息，woker进程收到信息后，更新进程内部的线程信息。（有点valatile的味道）

    方案二：
    修改配置文件nginx.conf后，重新生成新的worker进程，当然会以新的配置进行处理请求，而且新的请求必须都交给新的worker进程，至于老的worker进程，等把那些以前的请求处理完毕后，kill掉即可。

    Nginx采用的就是方案二来达到热部署的！
    ```
2. Nginx挂了怎么办? 
   ```
    Nginx既然作为入口网关，很重要，如果出现单点问题，显然是不可接受的。
    答案是：Keepalived+Nginx实现高可用。
    Keepalived是一个高可用解决方案，主要是用来防止服务器单点发生故障，可以通过和Nginx配合来实现Web服务的高可用。（其实，Keepalived不仅仅可以和Nginx配合，还可以和很多其他服务配合）
    Keepalived+Nginx实现高可用的思路：
    第一：请求不要直接打到Nginx上，应该先通过Keepalived（这就是所谓虚拟IP，VIP）
    第二：Keepalived应该能监控Nginx的生命状态（提供一个用户自定义的脚本，定期检查Nginx进程状态，进行权重变化,，从而实现Nginx故障切换）
   ```

安装Nginx相关小命令:
=======
1. 查看Linux版本: `uname -a` 
2. 安装GCC编译器: `yum install -y gcc` 因为Nginx是C代码编写的
3. 安装G++编译器: `yum install -y gcc-c++` 安装C++可以编写 Nginx HTTP模块
4. 安装PCRE库 正则表达式: `yum install -y pcre pcre-devel` 如果在nigin.conf的文件内使用了正则表达式，那么需要引入这个PCRE库才可以使用正则。这里要注意的是 pcre-devel 是二次开发所需要的库。
5. 安装zlib: `yum install -y zlib-devel` zlib库用于对HTTP包的内容做gzip格式的压缩，如果我们在`nginx.conf`里面配置了gzip on，并指定对于某些类型(content-type)的HTTP相应使用gzip进来进行压缩以减少网络传输量，那么，在编译时就需要把zlib编译进Nginx。
6. OpenSSL: `yum install -y openssl openssl-devel`，如果我们的服务器不止要支持HTTP，还需要在安全的SSL协议上传输HTTP，那么就需要OpenSSL了，另外，如果我们想使用MD5，SHA1等散列函数，那么也需要安装它。



磁盘目录:
=======
> Nginx的磁盘目录结构

1. Nginx **`源代码`** 存放目录: 该目录用于存放从官网下载的Nginx源码文件，以及第三方或我们自己所写的模块。

2. Nginx 编译阶段产生的中间文件存放目录: 该目录用于放置在Configure命令执行后所产生的源文件及目录，以及make命令执行后生成的目标文件和最终连接成功的二进制文件，默认情况下，configure命令会讲该目录命名为objs，并且放Nginx源代码目录下。

3. 部署目录: 该目录存放实际Nginx服务运行期间所需要的二进制文件，配置文件等，默认情况下，该目录为 /usr/local/nginx
    
4. 日志文件存放目录: 日志文件通常会比较大，当研究Nginx的底层架构时，需要打开debug级别的日志，这个级别的日志非常详细，会导致日志的文件大小增长很快，需要预先分配一个拥有更大磁盘空间的目录。


Nginx常用命令:
=======

1. 默认方式启动 `service nginx start`
2. 重启 `nginx -s reload`
3. 关闭 `nginx -s stop`
4. 查看端口 `netstat -an | grep 端口`
5. 查看进程 `ps -ef | grep nginx`
6. 查看错误日志 `tail -n30 /var/log/nginx/error.log`
7. 查看nginx.conf配置文件位置 `nginx -t`

Nginx配置文件:
=======
1. 反向代理
    ```
        server {
            listen       80;
            server_name  127.0.0.1;
            client_max_body_size    100m;
            location /api {
                proxy_pass   http://b.domain.com:9000;  # 最终地址会加上/api，变成 /api/xxx
                #proxy_cookie_domain   b.domain.com a.domain.com; # 需要修改接口返回的cookie域名时使用
            }
        }
    ```
2. 负载均衡
   ```
   upstream apiServer { 
        server 10.0.0.80:5000;  # 如果需要权重加 weight=数字
        server 10.0.0.81:5000;
    }
    server {
        listen       80;
        server_name  127.0.0.1;

        location /api {
            proxy_pass   http://apiServer;
        }
    }
   ```
3. 允许他站跨域访问
   ```
        server {
            listen       80;
            server_name  127.0.0.1;
            client_max_body_size    100m;
            location /api {
                proxy_pass   http://b.domain.com:9000;  # 最终地址会加上/api，变成 /api/xxx
                #proxy_cookie_domain   b.domain.com a.domain.com; # 需要修改接口返回的cookie域名时使用
                add_header Access-Control-Allow-Origin *;  # *表示允许所有站跨域访问（不安全，建议指定具体允许的域名如：http://b.domain.com:9000（注意格式：http(s):// + domain + port，末尾也不能加/）
                add_header Access-Control-Allow-Credentials true;  #此项为允许带cookie跨域访问，若设置true，上面域名配置不能为*，必须指定具体域名
            }
        }
   ```

4. 开启gzip压缩
   ```
   http {
    include          mime.conf;
    default_type     application/octet-stream;
    ....

    gzip             on;
    gzip_min_length  10k;
    gzip_comp_level  5;
    gzip_types       text/plain text/css application/x-javascript application/javascript text/javascript;

    server {
        ....
    }
   ```
5. 需要修改nginx.conf里的user，比如user root;


总结:
=======
文章大部分内容都是参考其他文章和书本，仅作为记录。如有错误请留言指正。 