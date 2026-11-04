---
layout: post
title: "Pikachu - Unsafe Filedownload 不安全文件下载"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, 文件下载, 任意文件下载]
permalink: /pikachu/unsafe-filedownload.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：Unsafe Filedownload -> 不安全的文件下载
- 难度：简单
- 通关标识：修改下载参数，利用目录穿越成功下载服务器配置文件或系统文件。

## 二、解题步骤

**1. 抓包分析**
开启 Burp Suite 拦截，点击“科比”图片，抓取请求：
```http
GET /pikachu/vul/unsafedownload/execdownload.php?filename=kb.png
