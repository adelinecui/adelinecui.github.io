---
layout: post
title: "Pikachu - Unsafe Fileupload getimagesize"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, 文件上传, 图片马, getimagesize]
permalink: /pikachu/upload-getimagesize.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：Unsafe Fileupload -> getimagesize()
- 难度：中等
- 通关标识：制作图片马绕过 `getimagesize()` 检测，并结合文件包含漏洞执行代码。

## 二、大白话原理讲解
这一关的服务器很聪明，会用 `getimagesize()` 函数检查上传文件的真实结构。直接复制粘贴代码会破坏图片格式，所以我们需要用 Windows 的 `copy` 命令，把 PHP 代码“无缝拼接”到图片末尾，保证图片能正常显示，从而骗过检查。

## 三、解题步骤

**1. 制作图片马**
准备一张正常图片 `1.jpg` 和木马文件 `shell.php`。在桌面打开命令行，执行：
```cmd
copy /b 1.jpg + shell.php shell.jpg
