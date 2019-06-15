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

