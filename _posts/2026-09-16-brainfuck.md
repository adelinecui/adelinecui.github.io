---
layout: post
title: "Bugku Crypto — [+-<>] WriteUp"
date: 2026-09-16
categories: bugku
---
# Bugku Crypto [+-<>]
## 题目信息
平台：Bugku CTF Crypto
题目描述：一串由 `+ - < > [ ] . ,` 组成的代码
考察知识点：Brainfuck编程语言解码

## 解题思路
字符只有`+‑<>[].,`，是Brainfuck语言。复制全部代码，使用Brainfuck在线解释器运行代码，直接输出flag。Bugku平台自带bf解密工具，可以直接使用。

## 操作步骤
1. 复制题目描述里面全部Brainfuck代码。
2. 打开Bugku自带Brainfuck工具或者在线bf解码器。
3. 将完整代码粘贴输入框，执行运行/解密。
4. 运行直接输出flag，复制结果提交。

## 遇到的坑
1. 复制代码不能少字符、不能漏括号，少一个符号运行就报错。
2. 不要带入多余换行空格，尽量完整复制原始密文。

## 知识点总结
1. Brainfuck标志性字符：`+‑<>[].,`，看到这套符号就直接用bf解码器。
2. 它属于极简编程语言，直接执行代码输出明文，不是传统密码。

>最终flag：`flag{0d86208ac54fbf12}`
