---
title: "[DB] 简单使用MySQL"
catalog: true
toc_nav_num: true
date: 2019-05-14 11:18:30
subtitle: "掌握简单的MySQL的命令，来操作数据库。"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/nature.jpg"
tags:
- DB
catagories:
- DB
---

> 当前环境: mysql  Ver 8.0.16 for osx10.14 on x86_64 (Homebrew)

常用的命令
=======

> SQL 不区分大小写，用小写也可以。

### 开启/关闭/查看状态
1. `mysql.server status` 查看数据库状态
2. `mysql.server start` 开启数据库
3. `mysql.server stop` 关闭数据库

### SHOW/USE
1. `mysql -u<用户名字> -p<密码>` 登陆
2. `SHOW DATABASES;` 展示所以数据库
3. `USE <数据库名字>;` 使用某一个数据库 成功会提示: Database changed
4. `SHOW TABLES;` 展示当前数据库下所有的表单
5. `SHOW COLUMNS FROM <表单名字>;` 展示表单结构

### SELECT 检索数据
1. `SELECT <表单里面的属性名字> FROM <表单名字>;` 查找这个属性从这个表单
2. `SELECT <表单里面的属性名字>, <表单里面的属性名字> FROM <表单名字>;` 查找多个属性从这个表单
3. `SELECT * FROM component;` 检索所有列
4. `SELECT DISTINCT <表单里面的属性名字> FROM <表单名字>;` 检索出ID不同的值从表单中，相同会被过滤掉。
5. `SELECT <表单里面的属性名字> FROM <表单名字> LIMIT <限制的个数 例如: 2>;` 检索结果限制个数
6. `SELECT <表单里面的属性名字> FROM <表单名字> LIMIT <从第几行开始 例如: 2>,<输出几行 例如: 1>;` 检索结果限制个数 LIMIT 后面第一个数开始位置，第二个数为要检索的行数。

### SELECT 排序检索数据
> a与A顺序相同么？ 在MySql里面默认是相同的，但是也可以让他不同，需要在数据库里面进行设置，使用ORDER BY子句是做不到的，需要请求数据库管理员帮助。
1. `SELECT <表单里面的属性名字> FROM <表单名字> ORDER BY <表单里面的属性名字>;` 对此属性进行排序 (默认升序)
2. `SELECT <表单里面的属性名字1>,<表单里面的属性名字2> FROM <表单名字> ORDER BY <表单里面的属性名字1>;` 对表单属性1进行排序，属性2不进行排序。（(默认升序)）
3. `SELECT <表单里面的属性名字1>,<表单里面的属性名字2> FROM <表单名字> ORDER BY <表单里面的属性名字1> DESC;` 对表单属性1进行排序，属性2不进行排序。 参数DESC降序排序 从Z->A
4. `SELECT <表单里面的属性名字1>,<表单里面的属性名字2>,<表单里面的属性名字3> FROM <表单名字> ORDER BY <表单里面的属性名字1> DESC, <表单里面的属性名字2>;` 对属性1进行倒序，属性2进行正序。
5. `SELECT <表单里面的属性名字1> FROM <表单名字> ORDER BY <表单里面的属性名字1> DESC LIMIT 1` 使用ORDER和LIMIT的组合进行检索







