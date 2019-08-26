---
title: "[CI/CD] 使用Travis持续部署Hexo项目"
catalog: true
toc_nav_num: true
date: 2019-06-15 16:30:30
subtitle: "如何使用Travis持续部署Hexo的项目"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- CI/CD
catagories:
- CI/CD
---

前言
=======

上一篇已经讲过了 如何使用Travis持续部署GitBook的项目，这篇我们来讲一下，我们如何持续部署我们的Hexo项目，我们想达到的目的就是，每次我写完文章上传到git后，自动帮去部署静态页面，更新博客，不需要我再手动调用部署命令来部署项目。

这篇文章我就直接上代码了，如果之前没搞过可以先看下我的上一篇文章 https://zcoldwater.github.io/blog/article/ci/travisci/


正文
=======

```yml
# 指定环境语言
language: node_js
# 指定sudo权限
sudo: required
# 指定node版本
node_js: stable
# 指定缓存模块，缓存可以加速编译
cache:
  directories:
    - node_modules

# 邮件通知，这里配置不管成功或是失败都通知
notifications:
  email:
    recipients:
      - yongpeng.zhu@nio.com
    on_success: always # default: change
    on_failure: always # default: always

# 构建的分支
branches:
  only:
    - master 

# 调整时区 安装hexo
before_install:
  - export TZ='Asia/Shanghai'
  - npm install -g hexo-cli  

# 安装环境
install:
  - npm install
  - npm install hexo-deployer-git --save

# 执行清缓存，生成网页操作
script:

# 生成文件后，进入静态文件目录，部署到指定仓库，需要填写用户名，邮件
after_script:
  - git config user.name "yongpeng.zhu"
  - git config user.email "1486297824@qq.com"
  - sed -i "s/gh_password/${GH_PASSWORD}/g" ./_config.yml # 这里使用 sed 命令是 替换 hexo 根目录的 _config.yml 文件里面的 gh_password 到 {GH_PASSWORD}，这样 Travis 执行脚本的时候 就可以使用 GH_PASSWORD 这个变量了。 GH_PASSWORD这个变量要去 Travis后台创建哦！ 上一篇文章有介绍。
  - hexo generate --deploy
# 环境变量
env:
 global:
   # Github Pages 
   - GH_REF: github.com/zColdWater/springlearning.git
```

结论
=======
如果你读过我的第一篇文章 https://zcoldwater.github.io/blog/article/ci/travisci/ 我相信这篇文章 你看下正文的脚本就OK了。  
现在我只需提交代码到 git 就自动更新了。
