---
layout: post
title: "Bugku Crypto — 抄错的字符 WriteUp"
date: 2026-09-16
categories: CTF Crypto
---
# Bugku Crypto 抄错的字符
## 题目信息
平台：Bugku CTF Crypto
题目描述：小明抄写把数字抄成大写字母，密文：`QWIHBLGZZXJSXZNVBZW`
考察知识点：Base64、字符替换还原

## 解题思路
题目提示数字被抄写成大写字母，整体是Base64编码。
I对应数字1，B对应6，G对应9，Z对应2，S对应5，还原字符，末尾补充base64填充符号`=`，得到正确密文再解码。

## 操作步骤
1. 拿到题目密文 `QWIHBLGZZXJSXZNVBZW`。
2. 根据题意替换错写字符，得到正确密文：`QW1hbl92ZXJ5X2Nvb2w=`。
3. 使用Base64解码工具进行解码。
4. 解码得到明文，组装成标准flag格式提交。

## 遇到的坑
1. 直接对原题密文解码会乱码，必须先还原被抄错的数字。
2. Base64长度需要被4整除，末尾要加上填充符`=`。
3. 注意区分字母和容易混淆的数字：1/I、6/B、9/G、2/Z、5/S。

## 知识点总结
1. Base64编码允许大小写字母、数字，部分字符外形很像，容易抄写混淆。
2. Base64编码串长度必须是4的倍数，不足时用`=`填充。

>最终flag：`flag{Aman_very_cool}`
