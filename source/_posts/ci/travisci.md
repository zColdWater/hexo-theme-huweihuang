---
title: "[CI] 使用Travis持续部署GitBook"
catalog: true
toc_nav_num: true
date: 2019-06-15 13:00:30
subtitle: "如何使用Travis持续部署GitBook"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- CI
catagories:
- CI
---

前言
=======

作为代码写的不咋地，倒是喜欢写博客记东西的我，肯定不会放过把刚学到的东西记录下来，希望能对有同样问题的同学有所帮助。

### **`Travis CI:`** 

    Travis CI是一个托管的 持续集成服务，用于构建和测试在GitHub上托管的软件项目。

    开源项目可以通过travis-ci.org免费测试。私人项目可能会在travis-ci.com上进行测试。TravisPro在客户自己的硬件上提供专有版本的自定义部署。

    源代码是技术上免费的软件，并且在许可许可下可以在GitHub上零碎地获得。但是，该公司指出，用户需要监视和执行的大量任务可能会使某些用户难以成功地将企业版与其自己的基础架构集成

总结一下: 

    1.有免费，有收费 版本。 
    2.只能是github上面的项目才可以使用。


### **`Travis CI 与 Jenkins 对比:`**
    优点:
    1. UI风格好看
    2. 对于GitHub上面的项目支持非常好，非常方便。
   
    缺点
    1. 只能支持GitHub项目。
    2. 项目不能本地部署，像一些公司级项目，安全上就要考量了。

以上是我粗浅的理解对比的区别，具体的细节，我也不是特别清楚，要深入使用过才知道。



正文
=======

1. 进入: https://travis-ci.com/ 
2. 登录:
   <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_login.png" height="350" />
3. 进入dashboard: 这里可以看见，我的项目有 `绿色的(脚本运行成功)`，`红色的(脚本运行失败)`，`灰色的(未配置.travis.yml文件的项目)`，其实还有黄色(正在运行的脚本)。 
   <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_dashboard.png" height="350" /> 
4. 在Travis后台设置项目环境变量:
     <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_setting.png" height="350" />
     
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_ environment.png" height="350" />
6. 项目配置 `.travis.yml` 文件，例如下面我的文件。
   <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_create.png" height="350" />
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
        - 1486297824@qq.com
        on_success: always # default: change
        on_failure: always # default: always

    # 构建的分支
    branches:
    only:
        - master 

    # 调整时区
    before_install:
    - export TZ='Asia/Shanghai'

    # 安装环境
    install:
    - npm install -g gitbook-cli
    - gitbook install

    # gitbook生成静态文件
    script:
    - gitbook build

    # 生成文件后，进入静态文件目录，部署到指定仓库，需要填写用户名，邮件
    # 这里要说的是 GH_USER 和 GH_PASSWORD 这两个变量是 在`Travis`后台设置。 正常来说 使用GitHub Token 也可以，但是由于我这边有些问题，没有配置 Token，建议大家用Token来配置 git remote 的源。
    after_script:
    - cp ./seo/* ./_book/
    - cd ./_book
    - git init
    - git config user.name "yongpeng.zhu"
    - git config user.email "1486297824@qq.com"
    - git add .
    - git commit -m "Update GitBook By TravisCI With Build $TRAVIS_BUILD_NUMBER"
    - git remote rm origin
    - git remote add origin https://${GH_USER}:${GH_PASSWORD}@github.com/zColdWater/springlearning.git
    - git push --force --quiet origin master:master

    # 环境变量
    env:
    global:
    # Github Pages 
    - GH_REF: github.com/zColdWater/springlearning.git
   ```
7. 编辑文章，然后上传到GitHub。
   <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_gitbook.png" height="350" />
   * README.md
        ```md
        # Introduction 我来试试!
        ```
   * SUMMARY.md
        ```md
        # Summary
         * [介绍](README.md)
         * [后端](./backend/README.md)
         * [小程序接口 api](./backend/smartprograme.md)
         * [小程序接口 api](./backend/smartprograme.md)
         * [前端规范](./frontend/README.md)
         * [js规范](./frontend/jsStandard.md)
        ```
    然后提交这些内容到你的仓库里面，当你执行完 `git push` ，`Travis`里面默认设置监听的 `Master` 分支，如果你推了代码到服务端，你再去 `Travis` 后台看下状态。
    <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/travis_running.png" height="350" />