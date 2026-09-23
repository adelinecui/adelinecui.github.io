---
layout: post
title: "Bugku MISC — linux WriteUp"
date: 2026-09-22
categories: bugku
tags: Bugku,CTF,MISC,linux,文件分析
---

# Bugku MISC linux
## 题目信息
平台：Bugku CTF MISC
题目描述：linux基础问题
提示：key{}
考察：**Linux基础命令与文件二进制检索**（解释：利用Linux字符串提取命令或者十六进制编辑器，在二进制文件中搜索指定关键字，找到隐藏文本）

## 解题思路
本题是MISC杂项入门题，下载附件是压缩包，解压得到二进制文件。
可以使用两种思路：
1. Linux环境下使用`strings`命令提取文件中所有可读字符串，搜索关键词`key`直接找到flag。
2. Windows下使用010 Editor/Notepad++打开文件，搜索字符串`key`定位flag。

## 操作步骤
1. 点击下载，获取题目附件压缩包，解压得到目标文件。
2. 方法1：Linux终端操作
```bash
# 提取文件内所有可打印字符串
strings flag | grep key
```
3. 方法2：Windows下使用010 Editor打开文件，使用查找功能搜索关键词`key`。
4. 搜索结果得到flag：`key{feb81d3834e2423c9903f4755464060b}`
```text
key{feb81d3834e2423c9903f4755464060b}
```
5. 将flag填入输入框提交，题目完成。
```text
提交成功
```

## 使用工具
```text
strings：Linux自带命令，用于提取二进制文件中的可打印字符串
010 Editor：十六进制编辑器，可以查看、搜索二进制文件内容
grep：Linux文本搜索工具，筛选匹配关键字的行
```

## 知识点&名词解释
1. **strings命令**：Linux基础工具，扫描二进制文件，输出里面所有ASCII可读字符串，常用于MISC查找隐藏文本。
2. **grep命令**：Linux文本过滤工具，按关键字匹配，筛选出包含目标字符串的行。
3. **MISC杂项**：CTF一大题型，不局限代码逆向/网页漏洞，包含文件隐写、流量分析、图片、系统文件等各类趣味题目。
4. **二进制文件**：非纯文本文件，普通记事本打开会显示乱码，需要专用工具查看内部原始字节。

## 总结
这道MISC题目考察Linux基础命令strings，属于入门文件检索题。核心思路：二进制文件中藏有可读字符串，使用strings提取，配合grep过滤关键词快速定位flag。Windows环境也可以使用十六进制编辑器直接搜索，不需要Linux环境。
