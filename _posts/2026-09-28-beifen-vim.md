---
layout: post
title: "CTFHub 信息泄露 - 备份文件下载-vim"
date: 2026-09-28
categories: ctfhub
tags: [ctfhub,信息泄露,vim备份,swp文件]
permalink: /ctfhub/beifen-vim.html
---

# CTFHub 信息泄露 - 备份文件下载-vim
## 一、题目信息
平台：CTFHub
分类：Web - 信息泄露
题目名称：备份文件下载 - vim
难度：简单

## 二、考点
1. vim编辑器交换文件 `.swp` 信息泄露
2. 常见编辑器备份文件后缀识别

## 三、原理分析
vim编辑器在编辑文件时，会自动生成交换文件`.swp`，用来临时保存文件内容，防止程序意外退出导致文件丢失。
如果网站服务器没有对这类隐藏文件做访问限制，攻击者就可以直接访问、下载`.swp`文件，获取网站源代码。
vim相关备份后缀：
- `.swp`：正在编辑生成的交换文件（本题使用）
- `.swo`：更早版本的交换文件
- `~`：vim简单备份文件

## 四、解题过程
打开题目页面，页面提示：`flag 在 index.php 源码中`。
直接访问`index.php`只能看到网页渲染后的页面，看不到PHP源码。
根据vim备份文件知识点，尝试访问交换文件地址：
`http://challenge-xxxx.sandbox.ctfhub.com:10800/.index.php.swp`
> 重点：文件名开头**带小数点**，少写小数点会返回Not Found（404）。

访问成功后，浏览器会下载`.index.php.swp`文件。
使用记事本打开swp文件，里面包含`index.php`完整源代码，在源码中找到flag。

## 五、Flag
`ctfhub{2edbcdc0214de566fb4d5636}`

## 六、总结
遇到源码提示类信息泄露题目，可以优先尝试vim交换文件`.swp`。
注意文件名称前面的小数点，这是最容易踩坑的地方。
