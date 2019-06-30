---
title: "[SHELL] SHELL基础"
catalog: true
toc_nav_num: true
date: 2019-06-30 20:00:30
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
4. `your_name="runoob.com"` 例如这里，your_name 就是变量名。  
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
5. **for file in `ls /etc`** or **for file in $(ls /etc):** 以上语句将 /etc 下目录的文件名循环出来。


变量
=======
> 使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

### **赋值**
```java
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

### **只读变量**
```java
#!/bin/bash
myUrl="http://www.google.com"
readonly myUrl
myUrl="http://www.runoob.com"

执行结果: /bin/sh: NAME: This variable is read only.
```

### **删除变量**
```java
unset variable_name
```
例如:   
```java
#!/bin/sh
myUrl="http://www.runoob.com"
unset myUrl
echo $myUrl

以上实例执行将没有任何输出。
```

### **变量类型**
1. **局部变量** 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2. **环境变量** 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3. **shell变量** shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行



字符串
=======

### **单引号**
```java
str='this is a string'
```

### **双引号**
```java
your_name='runoob'
str="Hello, I know you are \"$your_name\"! \n"
echo -e $str

// 输出结果 Hello, I know you are "runoob"! 
```

### **拼接字符串**
```java
your_name="runoob"
# 使用双引号拼接
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting  $greeting_1
# 使用单引号拼接
greeting_2='hello, '$your_name' !'
greeting_3='hello, ${your_name} !'
echo $greeting_2  $greeting_3

// 输出结果
// hello, runoob ! hello, runoob !
// hello, runoob ! hello, ${your_name} !
```

### **获取字符串长度**
```java
string="abcd"
echo ${#string} #输出 4
```

### **提取子字符串**
```java
string="runoob is a great site"
echo ${string:1:4} # 输出 unoo
```

### **查找子字符串**
```java
string="runoob is a great site"
echo `expr index "$string" io`  # 输出 4
// 注意： 以上脚本中 ` 是反引号，而不是单引号 '，不要看错了哦。
```


数组
=======

### **定义数组**
```java
1.array_name=(value0 value1 value2 value3)
2.my_array=(A B "C" D)

或者

array_name=(
value0
value1
value2
value3
)

或者

array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen

```

### **读取数组**

```java
1.valuen=${array_name[n]}
2.${array_name[index]}

或者

echo ${array_name[@]} // 使用 @ 符号可以获取数组中的所有元素，例如：
```

### **获取数组的长度**
```java
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```



传递参数
=======

> 我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：$n。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推……

参数处理| 说明 
--- | --- 
$#	 | 传递到脚本的参数个数 
$*	 | 以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
$$	 | 脚本运行的当前进程ID号
$!	 | 后台运行的最后一个进程的ID号
$@	 | 与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
$-	 | 显示Shell使用的当前选项，与set命令功能相同。
$?	 | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。  


### **实例**
> 获取传入的参数
```java
// Shell脚本
#!/bin/bash
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```

```java
// 执行以下命令
$ chmod +x test.sh 
$ ./test.sh 1 2 3
Shell 传递参数实例！
执行的文件名：./test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3
```

基本运算符
=======

> 普及 Linux expr 命令 

 1、**`计算字串长度`**  
 expr length “this is a test”  
 2、**`抓取字串`**  
 expr substr “this is a test” 3 5  
 3、**`抓取第一个字符数字串出现的位置`**  
 expr index "sarasara"  a  
 4、**`整数运算`**  

    > expr 14 % 9
    5
    > expr 10 + 10
    20
    > expr 1000 + 900
    1900
    > expr 30 / 3 / 2
    5
    > expr 30 \* 3 (使用乘号时，必须用反斜线屏蔽其特定含义。因为shell可能会误解显示星号的意义)
    90
    > expr 30 * 3
    expr: Syntax error  

### **算术运算符**

运算符	| 说明 | 举例 
--- | --- | ---
+	 | 加法 | `expr $a + $b` 结果为 30。
-	 | 减法 | `expr $a - $b` 结果为 -10。
*	 | 乘法 | `expr $a \* $b` 结果为  200。
/	 | 除法 | `expr $b / $a` 结果为 2。
%	 | 取余	| `expr $b % $a` 结果为 0。
=	 | 赋值	| a=$b 将把变量 b 的值赋给 a。
==	 | 相等。用于比较两个数字，相同则返回 true | [ $a == $b ] 返回 false。
!=	 | 不相等。用于比较两个数字，不相同则返回 true | [ $a != $b ] 返回 true。

例子: 
```java
#!/bin/bash

a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi

//输出
a + b : 30
a - b : -10
a * b : 200
b / a : 2
b % a : 0
a 不等于 b
```

