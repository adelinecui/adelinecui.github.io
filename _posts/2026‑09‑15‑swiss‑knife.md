---
layout: post
title: "Bugku PWN — 瑞士军刀 WriteUp"
date: 2026‑09‑15
categories: CTF PWN
---
# Bugku PWN 瑞士军刀
## 题目信息
平台：Bugku CTF PWN
题目描述：ls
考察：**nc(netcat) 瑞士军刀**（解释：netcat简称nc，被称为网络瑞士军刀，命令行工具，可以连接远程服务器端口，传输数据，PWN题型经常用来和远程交互shell）

## 解题思路
本题给了远程IP地址+端口号，需要用nc工具连接远程服务。连接成功后不会自动回显内容，直接输入读取文件命令`cat flag`，输出flag。

## 操作步骤
1. 启动场景，拿到远程IP和端口。
2. Linux终端打开，执行nc命令连接远程服务：
`nc IP地址 端口号`
3. 连接成功，屏幕看不到任何提示，直接输入命令：
`cat flag`
> cat：linux命令，读取文件内容；flag就是存放答案的文件。
4. 回车执行，输出flag字符串，复制提交。

## 遇到的坑
1. 连接之后没有任何欢迎文字，新手以为连接失败，其实已经进入交互，直接输入命令；
2. 不要多加多余引号、空格，命令输入错误无法读取flag；
3. Windows系统需要安装netcat工具，Kali Linux自带nc。

## 知识点总结
1. nc（netcat）网络瑞士军刀，用于TCP/UDP连接，PWN题目用来对接远程shell；
2. cat命令：Linux读取打印文件内容；
3. PWN题型特点：大多是连接远程端口交互，执行系统命令获取flag。
