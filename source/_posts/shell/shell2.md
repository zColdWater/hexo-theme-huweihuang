---
title: "[SHELL] SHELL基础"
catalog: true
toc_nav_num: true
date: 2019-06-30 14:00:30
subtitle: "这篇文章记录Shell基础语法"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/cover.jpg"
tags:
- SHELL
catagories:
- SHELL
---

前言
=======
1. shell 脚本必须有 一个标记 例如: `#!/bin/bash`
2. sh脚本需要添加权限 `chmod +x ./test.sh  #使脚本具有执行权限`
3. 执行脚本 `./test.sh  #执行脚本`


变量
=======
1. `your_name="runoob.com"` 例如这里，your_name 就是变量名。  
    **有效的 Shell 变量名示例如下：**
    ```
    RUNOOB
    LD_LIBRARY_PATH
    _var
    var2
    ```
    **无效的变量命名：**
    ```
    ?var=123
    user*name=runoob
    ```
2. **for file in `ls /etc`** or **for file in $(ls /etc):** 以上语句将 /etc 下目录的文件名循环出来。


使用变量
=======
> 使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

## **赋值**
```shell
例子一: 
your_name="qinjx"
echo $your_name
echo ${your_name}

例子二:
for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done

例子三
your_name="tom"
echo $your_name
your_name="alibaba" // 赋值again
echo $your_name
```

## **只读变量**
```shell
#!/bin/bash
myUrl="http://www.google.com"
readonly myUrl
myUrl="http://www.runoob.com"

执行结果: /bin/sh: NAME: This variable is read only.
```

## **删除变量**
```shell
unset variable_name
```
例如:   
```
#!/bin/sh
myUrl="http://www.runoob.com"
unset myUrl
echo $myUrl

以上实例执行将没有任何输出。
```