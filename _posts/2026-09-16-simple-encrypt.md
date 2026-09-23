---
layout: post
title: "Bugku Crypto — 简单加密 WriteUp"
date: 2026-09-16
categories: bugku
---
# Bugku Crypto 简单加密
## 题目信息
平台：Bugku CTF Crypto
题目描述：密文：`e6Z9i~]8R~U~QHE{RnY{QXg~QnQ{^XVIRXIp^XI5Q6Q6SKY8jUAA`，提示输出格式 `key{}`

考察知识点：ASCII偏移、Base64解码

## 解题思路
观察密文末尾是`AA`，Base64正常填充结尾是`==`。
A 的 ASCII 是65，`=` 的 ASCII 是61，两者相差4。
说明加密的时候每个字符 ASCII 数值 +4；解密就需要全部字符 ASCII -4，还原出标准Base64字符串，再进行Base64解码拿到flag。

## 操作步骤
1. 完整复制题目给出的密文。
2. 将密文中每一个字符的ASCII码减去4，还原成正常的base64编码串。
3. 使用Base64解码工具，对处理后的字符串解码。
4. 得到key{}格式的flag，复制提交。

## 遇到的坑
1. 不能直接进行Base64解密，字符整体偏移，直接解密会出现乱码。
2. 全部字符都要执行减4操作，不只是末尾两个字符。

## 知识点总结
1. ASCII偏移是凯撒密码的变形，对字符编码数值整体加减。
2. Base64末尾填充符号`==`经常作为解题突破口。

>最终flag：`key{68743000650173230e4a58ee153c68e8}`
