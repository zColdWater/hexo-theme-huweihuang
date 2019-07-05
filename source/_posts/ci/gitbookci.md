---
title: "[CI] 使用WebHook持续部署GitBook"
catalog: true
toc_nav_num: true
date: 2019-07-05 16:19:30
subtitle: "记录如何使用WebHook进行持续部署"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- CI
catagories:
- CI
---

背景
=======

作为一名有"痣"青年，一直致力于追求最帅最靓的文档，然而天不长眼 公司既然花钱部署了一个叫 `Confluence`的家伙，开始寻思为了生活就算了 虽然之前没用过，但看着也还凑合，但随着后面大量使用的时候，我们才发现，我货对`markdown`的支持实在太差了，部署在我们公司的 `Confluence` 根本不能用 "马克党"，在坚持了快2月左右的情况下，全组成员 基本全军覆膜，文档写起来超级麻烦不说，还不好排版，因为根本用不惯`WiKi`的格式。 大家心理都暗自骂娘。  

最后大家觉得，都是搞技术的，什么事情是可以难到程序猴的，答案肯定是 不可能滴！，我就随口说了句 `gitbook` 怎么样，万万没想到，大家就这么愉快的同意了，当然搭建`gitbook`的责任就落在我身上啦。
经过努力，我们终于搞定了这一切，用起来是真滴舒服。 不光帅 还漂亮，还有`git`管理 有历史，根本不怕文档丢了，或者怎么样。

分析需求
=======

1. 搭建`gitbook`
2. 多人编辑后提交并且 push 代码到远端，自动触发构建部署任务。
3. 其他组小伙伴提MR，也要自动触发构建部署任务。 

下面说下我们想要做的事情的大致流程:
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook9.png" height="350" />

首先，我们的小伙伴开发文档在本地，然后推到远端，远端接到Push/MergeRequest的时间，Hook到转发给我们的Node服务处理，将远端仓库更新至服务器端，然后build部署。 用户就可以看见最新的文档了。 对于我们的文档开发者来说，做的只需要提交代码到远端即可。 

解决需求
=======

1. 搭建`gitbook`到本地服务器，我想这是最简单的了。 
   1. npm install gitbook-cli -g
   2. gitbook init
   3. gitbook build  # 执行这个命令后 会生成 _book目录，进入_book目录 然后打开index.html文件，就是我们文档。 

2. 当 git `Push` `MergeRequest` 的时候触发自动构建。 这里就用到了 `Webhook`工具，一句话 `Webhook`工具就是 hook 各种git操作的工具，比如 hook 住`git push`的操作，然后回调给你服务器的API。 举个例子，A同学操作仓库1 ->push 仓库1-> gitlab hook -> 调用你填入的API -> 你的Server接到了调用，知道是仓库有push的操作了，然后执行一些事情。 **备注: webhook的工具，gitlab，github 都有集成，所以如果您在这两个上面维护代码，配置下就可以使用了。** 
    1) 如何配置`WebHook`，我用几张图来演示。
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook1.png" height="350" />  
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook2.png" height="350" />
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook3.png" height="350" />  
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook6.png" height="350" />

        Node服务项目的 app.js 文件，我的这个项目中需要使用两个JS库，一个是用来接收 Git Hook 之后发来的请求的，一个是用来记录日志的。

        1. `npm install log4js`   #日志
        2. `npm install node-gitlab-webhook --save` #webhook-gitlab

        ```js
        #### app.js 文件内容 ####

        var log4js = require('log4js');
        var logger = log4js.getLogger();

        // 这个变量是为了给每次触发一个不同的标记，因为我们要把这个 数字传入到触发的脚本里面，脚本对应这个数字标记创建对应的文件夹，之所以这么做完全是因为，短时间内有好几个push事件，那么脚本如果对同一个文件夹做 乱序的命令会出问题，所以用这个全局的 数字做了标记，换句话说，每一次操作 都在操作一个不同的文件夹。
        global.globalNum = 1; 

        logger.level = "info"; // default level is OFF - which means no logs at all.

        function execFunc(content) {
        var exec = require('child_process').exec
        exec(content, function(error, stdout, stderr) {
            if (error) {
            logger.info('exec error:' + error)
            return
            }
            logger.info('stdout:' + stdout)
            logger.info('stderr:' + stderr)
        })
        }

        var http = require('http')
        var createHandler = require('node-gitlab-webhook')
        var handler = createHandler({ path: '/webhook1', secret: 'secret1' })
        http.createServer(function (req, res) {
        logger.info("createServer");
        handler(req, res, function (err) {
            logger.info("404 error");
            res.statusCode = 404
            res.end('no such location')
        })
        }).listen(7777)

        handler.on('error', function (err) {
        logger.info('Error:', err.message)
        })

        // 接收 push 事件
        handler.on('push', function (event) {
        logger.info(event.payload.project.git_ssh_url)
        logger.info(JSON.stringify(event.payload))
        logger.info('Received a push event for %s to %s',
            event.payload.repository.name,
            event.payload.ref);
        switch (event.path) {
            case '/webhook1':
                global.globalNum += 1;
                logger.info(global.globalNum)
                // 执行要做的事情，例如我这里 执行我的脚本来部署 gitbook
                execFunc('sh ./deploy.sh '+ global.globalNum)
                break
            default:
            break
        }
        })

        // 接收 merge_request 事件
        handler.on('merge_request', function (event) {
        logger.info(event.payload.project.git_ssh_url)
        logger.info(JSON.stringify(event.payload))
        logger.info('Received a merge_request event for %s to %s',
            event.payload.repository.name,
            event.payload.ref);
        switch (event.path) {
            case '/webhook1':
                global.globalNum += 1;
                logger.info(global.globalNum)
                // 执行要做的事情，例如我这里 执行我的脚本来部署 gitbook
                execFunc('sh ./deploy.sh '+ global.globalNum)
                break
            default:
            break
        }
        })
        ```
        搞定Node服务后，开启服务，你可以使用`pm2`命令挂在后台，前提是你的服务是OK的，调试的时候你就先 执行 `node app.js` 调试没有问题了，在后台部署挂起，然后你就可以Test你的WebHook看看是否有效，
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook4.png" height="350" />
        <img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook5.png" height="350" />

效果
=======
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/webhook8.png" height="350" />

结论
=======
经过我们的努力，终于达到我们的需求了，现在小伙伴们，只需要提交代码，就可以自动触发 `build`，然后过一会刷新下 服务器上的静态`gitbook`博客就被刷新了，是不是很省力气呢。
