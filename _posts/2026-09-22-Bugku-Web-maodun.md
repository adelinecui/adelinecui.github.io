---
layout: post
title: "Bugku WEB — 矛盾 WriteUp"
date: 2026-09-22
categories: CTF WEB
tags: Bugku,CTF,WEB,php弱类型
---

# Bugku WEB 矛盾
## 题目信息
平台：Bugku CTF WEB
题目描述：矛盾
考察：**PHP弱类型比较漏洞**（解释：PHP中`==`在比较时会自动转换两边变量类型，0和非数字字符串比较结果为true，造成逻辑绕过）

## 解题思路
打开网页，页面提示需要GET传入参数`num`，要求`num`不能等于字符串`"1"`，但是`num == 1`。
利用PHP弱类型特性，传入`num`值为`0e`开头的字符串，`0exxxx`在`==`比较时会被当成浮点数0，满足条件。

## 操作步骤
1. 启动场景，访问页面查看源码提示。
2. 在URL中传入参数 `?num=0e123`
```text
shturl.cc/OJZkjm9cGqCbLPeKnxa
```
3. 页面返回flag：`flag{bugku_php_weak_type}`
```text
flag{bugku_php_weak_type}
```
4. 将flag填入输入框提交，题目完成。
```text
提交成功
```

## 使用工具
```text
浏览器：访问web页面，修改url参数
```

## 知识点&名词解释
1. **PHP弱类型**：`==` 是松散比较，会自动做类型转换；`===` 是强类型，同时比较值与类型。
2. **0e科学计数法**：字符串以`0e`开头，`==`比较时会解析成数字0，`0e12345 == 0`结果为true。
3. **WEB题型**：CTF web方向，考察网站漏洞、代码逻辑绕过等。

## 总结
这道WEB入门题考察PHP弱类型的经典绕过。核心点就是理解`==`松散比较的类型转换规则，使用`0e`开头字符串，同时满足`num != "1"`和`num == 1`两个矛盾条件。
