---
layout: post
title: "CTFHub RCE - 命令注入"
date: 2026-10-29
categories: ctfhub
tags: [ctfhub,web,RCE,命令注入,无过滤,bypass]
permalink: /ctfhub/rce-command-injection.html
---

# CTFHub RCE - 命令注入

## 一、题目信息
平台：CTFHub
分类：Web - RCE
题目名称：命令注入
难度：简单
Flag：`ctfhub{a9eadebae1dd05f900ec11eb}`

## 二、考点
1. 命令注入漏洞，后端直接拼接用户输入到系统ping命令中
2. 命令分隔符 `;` 的使用，实现多条命令连续执行
3. PHP文件会被服务器解析，需要借助hexdump读取原始文件内容
4. Linux基础命令使用：ls、hexdump

## 三、题目内容
这是一个在线测试网络延迟的平台，类似路由器的ping功能，无任何安全措施。页面接收用户输入的IP地址，后端直接将输入拼接到ping系统命令执行，没有对输入内容做过滤，存在命令注入漏洞。直接cat读取php文件时，文件内容会被PHP引擎解析，无法查看源码。

## 四、解题步骤
1. 访问题目靶场，根据ping延迟测试功能，判断存在命令注入，使用`;`作为命令分隔符，构造payload：`127.0.0.1;ls`，列出当前目录文件。
2. 回显目录文件为`57862120410393.php`、`index.php`，尝试cat读取php文件，内容被PHP解析，无法获取flag。
3. 改用payload：`127.0.0.1;hexdump -C 57862120410393.php`，以十六进制形式读取文件，右侧附带ASCII明文。
4. 从hexdump输出的明文区域提取flag：`ctfhub{a9eadebae1dd05f900ec11eb}`。

## 五、知识点解析
1. 命令分隔符 `;`：无论前一条命令执行成功与否，`;` 后面的命令都会执行，同类分隔符还有`|`、`&&`、`||`。
2. hexdump命令：读取文件并输出十六进制+对应ASCII字符，不会被PHP解析，适合读取php源码文件。
3. PHP exec()：exec函数用来调用操作系统外部命令，当用户输入可控且无过滤时，产生远程命令执行漏洞。

## 六、总结
本题属于基础无过滤命令注入，归类在RCE板块，核心是利用命令分隔符拼接系统命令。本题的坑点是flag藏在php文件内，直接cat访问会被PHP解析，看不到注释里的flag，使用hexdump读取原始文件是解题关键。
防御上需要对用户输入做严格过滤，禁止传入命令分隔符，不直接将用户输入拼接进系统命令。
