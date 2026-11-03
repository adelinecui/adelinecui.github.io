---
layout: post
title: "Pikachu - SQL注入 insert/update 注入"
date: 2026-11-03
categories: pikachu
tags: [pikachu, web, sql注入, 二次注入, 报错注入]
permalink: /pikachu/sqli-insert-update.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：SQL-Inject -> "insert/update"注入
- 难度：中等
- 通关标识：利用 `updatexml` 函数触发报错，成功爆出数据库名

## 二、考点
1. 二次注入（Second-Order SQL Injection）原理
2. `updatexml()` 报错注入
3. 注释符与引号闭合的灵活运用

## 三、题目内容
页面提供注册、登录和修改密码功能。注册时，后端将数据插入数据库；修改密码时，后端从数据库取出用户名并拼接到 UPDATE 语句中。如果注册时输入了恶意 Payload，会在修改密码时被二次触发。

## 四、解题步骤

**1. 构造报错 Payload**
为了绕过可能的转义并在二次触发时直接报错获取数据，我们在注册页面构造如下 Payload 作为用户名：
```sql
1' or updatexml(1,concat(0x7e,database()),0) or '
```

**2. 注册并登录**
使用上述 Payload 作为用户名，密码随意（如 `123456`）进行注册。注册成功后，用该账号登录。

**3. 触发二次注入**
进入“修改个人信息”或“修改密码”页面。在提交修改时，后端会从数据库中取出刚才存入的用户名（也就是我们的 Payload），拼接进 SQL 语句执行。
此时，`updatexml()` 函数尝试解析 `~pikachu`（`0x7e` 是 `~` 的十六进制），因为 `~` 不是合法的 XPath 语法，导致数据库报错，并将数据库名打印在页面上。

**4. 获取结果**
页面成功回显：
```text
XPATH syntax error: '~pikachu'
```
成功获取当前数据库名为 `pikachu`。

## 五、知识点解析
1. **二次注入**：恶意代码被存入数据库，在后续的操作中被重新取出并执行。因为入库时可能被转义，但取出时没有再次过滤，导致漏洞。
2. **`updatexml()` 报错注入**：`updatexml(XML_document, XPath_string, new_value)`。当 XPath 参数包含非法字符时，MySQL 会抛出错误，错误信息中会包含这部分非法字符。攻击者利用 `concat()` 拼接查询结果（如 `database()`），即可在报错信息中获取数据。

## 六、总结与防御
**本题总结**：
利用报错注入可以绕过盲注的痛苦，直接在页面上获取数据。二次注入的核心在于“存储”与“触发”。

**安全防御建议**：
- **预编译语句**：彻底消除字符串拼接带来的注入风险。
- **统一转义**：无论数据来自前端还是数据库，在拼接 SQL 前都必须进行严格转义。
- **关闭错误回显**：在生产环境中关闭 `display_errors`，防止报错信息泄露敏感数据。
