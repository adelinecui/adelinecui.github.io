---
layout: post
title: "Pikachu - File Inclusion 远程文件包含(RFI)"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, 文件包含, RFI]
permalink: /pikachu/fi-remote.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：File Inclusion -> File Inclusion(remote)
- 难度：中等
- 通关标识：开启 `allow_url_include` 后，成功包含远程服务器上的文件。

## 二、题目内容
页面提示无法进行远程包含。这是因为 PHP 默认关闭了 `allow_url_include` 选项。开启该选项后，后端允许通过 URL 地址包含远程文件，从而引发远程文件包含漏洞（RFI）。

## 三、解题步骤

**1. 修改 PHP 配置**
打开 `php.ini`，将 `allow_url_include` 设置为 `On`，确保 `allow_url_fopen` 也为 `On`。重启 Web 服务器（如 phpStudy）。

**2. 抓包并修改参数**
开启 Burp Suite 拦截，在下拉框选择任意球员提交。将 `filename` 参数修改为指向远程文件的 URL：
```text
filename=http://www.baidu.com/robots.txt
```
*(这里使用网络上公开的纯文本文件作为测试，无需自己搭建服务器)*

**3. 验证结果**
点击放行，关闭拦截。页面成功回显了百度 `robots.txt` 文件的内容，证明远程文件包含漏洞利用成功。

## 四、知识点解析
1. **远程文件包含（RFI）**：当 `allow_url_include` 开启时，攻击者可以包含一个远程 URL 指向的恶意文件（如一句话木马），从而在目标服务器上执行任意代码。
2. **测试技巧**：在不方便自建恶意文件时，可以直接包含网络上公开的、确定存在的文本文件或正常网页，以此来验证漏洞是否存在。

## 五、总结与防御
**本题总结**：
远程文件包含漏洞的危害远大于本地包含，因为攻击者可以直接引入并执行恶意代码。

**安全防御建议**：
- **关闭危险配置**：保持 `allow_url_include = Off`。
- **白名单校验**：严格限制用户传入的文件名。
- **过滤协议**：过滤 `http://`、`https://`、`ftp://` 等远程协议头。
