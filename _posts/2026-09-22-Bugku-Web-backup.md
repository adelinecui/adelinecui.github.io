---
layout: post
title: "Bugku WEB — 备份是个好习惯 WriteUp"
date: 2026-09-22
categories: bugku
tags: Bugku,CTF,WEB,备份文件泄露
---

# Bugku WEB 备份是个好习惯
## 题目信息
平台：Bugku CTF WEB
题目描述：备份是个好习惯
考察：**网站备份文件泄露**（解释：网站开发时会留下源码备份，常见后缀.bak/.swp/.txt，访问备份文件可以直接拿到网站源码）

## 解题思路
启动场景打开网页，页面没有信息。根据题目提示“备份”，尝试访问index.php的备份文件`index.php.bak`，下载得到php源码，源码内包含flag。

## 操作步骤
1. 启动场景，访问网页。
2. 在url后拼接备份后缀，访问`index.php.bak`
```text
http://xxx.bugku.com/index.php.bak
```
3. 下载bak备份文件，打开查看源码，找到flag：`flag{backup_1s_g00d_habit}`
```text
flag{backup_1s_g00d_habit}
```
4. 将flag填入输入框提交，题目完成。
```text
提交成功
```

## 使用工具
```text
浏览器：访问网页，下载bak备份文件
记事本：打开bak文件查看php源码
```

## 知识点&名词解释
1. **备份文件泄露**：网站开发人员留下`.bak`、`.swp`、`.old`等源码备份文件，没有删除，攻击者直接下载读取源码。
2. **PHP源码泄露**：正常访问php会执行代码；访问`.bak`备份文件会直接下载原始源代码。
3. **WEB题型**：CTF web方向，信息收集、源码泄露、漏洞利用等。

## 总结
这道Web入门题，题面提示很明显。核心思路：看到“备份”，尝试常见备份后缀，直接下载源码备份文件读取flag。
