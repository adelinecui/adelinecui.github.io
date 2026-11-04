---
layout: post
title: "Pikachu - File Inclusion 本地文件包含(LFI)"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, 文件包含, LFI, 目录穿越]
permalink: /pikachu/fi-local.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：File Inclusion -> File Inclusion(local)
- 难度：简单
- 通关标识：利用目录穿越符 `../` 或 `php://filter` 伪协议，成功读取服务器本地文件。

## 二、题目内容
页面通过下拉框选择球员并提交。后端使用 `include()` 函数包含文件，但路径拼接存在缺陷（如 `include("include/" . $filename)`），导致本地文件包含漏洞。

## 三、解题步骤

**1. 抓包与初步测试**
开启 Burp Suite 拦截，选择任意球员提交。将参数修改为：
```text
filename=C:\Windows\win.ini
```
放行后页面报错，提示 `Failed opening 'include/C:\Windows\win.ini'`。说明后端强制拼接了 `include/` 目录。

**2. 目录穿越绕过**
使用 `../` 退出当前目录，构造 Payload：
```text
filename=../../../../../../Windows/win.ini
```
放行后，页面成功回显了 Windows 系统配置文件 `win.ini` 的内容。

**3. 进阶利用（伪协议读取源码）**
利用 `php://filter` 伪协议，可以在不回显代码的情况下读取源码并 Base64 编码：
```text
filename=php://filter/read=convert.base64-encode/resource=fi_local.php
```
获取 Base64 编码后解码，即可查看后端 PHP 源代码。

## 四、知识点解析
1. **本地文件包含（LFI）**：由于未对用户传入的文件路径进行严格校验，攻击者可以通过 `../` 穿越目录，读取服务器上的任意文件（如 `/etc/passwd` 或系统配置文件）。
2. **`php://filter` 伪协议**：常用来读取 PHP 源码。通过 `convert.base64-encode` 将源码进行 Base64 编码，避免代码被直接执行，从而让攻击者获取源码。

## 五、总结与防御
**本题总结**：
文件包含漏洞不仅可以读取系统文件，还能通过包含日志或上传的文件实现 RCE。

**安全防御建议**：
- **白名单校验**：严格限制用户只能包含指定的合法文件名，禁止传入路径符号。
- **过滤特殊字符**：过滤 `../`、`.\`、`php://` 等危险字符和协议。
- **关闭危险配置**：在 `php.ini` 中关闭 `allow_url_include` 和 `allow_url_fopen`。
