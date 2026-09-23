---
layout: post
title: "Bugku Reverse — 入门逆向 WriteUp"
date: 2026-09-22
categories: bugku
tags: Bugku,CTF,Reverse,静态逆向
---

# Bugku Reverse 入门逆向
## 题目信息
平台：Bugku CTF Reverse
题目描述：来源XJNU，下载附件分析程序，找到flag提交
考察：**静态逆向分析**（解释：不运行程序，直接对二进制可执行文件进行反汇编分析，查看汇编指令与伪代码来获取程序内部数据）

## 解题思路
本题是一道逆向入门CrackMe。直接运行程序只会输出一段提示信息，不会直接给出flag。
程序没有把flag存放在字符串常量区，而是在代码里逐字节将flag的ASCII十六进制值写入栈内存。F5查看伪代码无法直接看到flag，需要切换到汇编视图提取十六进制ASCII码，再转换成字符拼接得到flag。

## 操作步骤
1. 下载附件，使用Detect It Easy检测文件信息。
    文件为无壳32位PE程序，可以直接用IDA32打开分析。
2. 使用IDA Pro 32bit载入程序，等待自动分析完成，定位`main`函数。
3. 按下F5查看C伪代码，伪代码中只有打印语句，找不到flag字符串。
4. 按下Tab切换到汇编视图，查看main函数下方连续`mov byte ptr`指令，提取每一行后面的十六进制ASCII值。
```asm
mov byte ptr [esp+2Fh], 66h
mov byte ptr [esp+2Eh], 6Ch
mov byte ptr [esp+2Dh], 61h
mov byte ptr [esp+2Ch], 67h
mov byte ptr [esp+2Bh], 7Bh
mov byte ptr [esp+2Ah], 52h
mov byte ptr [esp+29h], 65h
mov byte ptr [esp+28h], 5Fh
mov byte ptr [esp+27h], 31h
mov byte ptr [esp+26h], 73h
mov byte ptr [esp+25h], 5Fh
mov byte ptr [esp+24h], 53h
mov byte ptr [esp+23h], 30h
mov byte ptr [esp+22h], 5Fh
mov byte ptr [esp+21h], 43h
mov byte ptr [esp+20h], 30h
mov byte ptr [esp+1Fh], 4Ch
mov byte ptr [esp+1Eh], 7Dh
```
5. 在IDA中选中十六进制数字，按快捷键R，将十六进制ASCII码转为字符。拼接所有字符得到flag：`flag{Re_1s_S0_C0OL}`
```python
# 转换脚本，把16进制ASCII转为字符
hex_list = ['66','6c','61','67','7b','52','65','5f','31','73','5f','53','30','5f','43','30','4c','7d']
result = ''.join([bytes.fromhex(h).decode('ascii') for h in hex_list])
print(result)
```
6. 将flag填入输入框提交，题目完成。
```text
flag{Re_1s_S0_C0OL}
```

## 使用工具
```text
Detect It Easy(DIE)：文件检测工具，识别PE文件位数，检测程序是否加壳
IDA Pro 32bit：静态反汇编工具，查看程序伪代码、汇编指令，分析二进制程序逻辑
```

## 知识点&名词解释
1. **PE文件**：Windows系统下的可执行文件格式，后缀一般为`.exe`。PE32代表32位程序，需要使用32位版本IDA打开。
2. **栈（Stack）**：程序运行时的一块临时内存空间。本题flag是运行时写入栈内存，不是存放在程序常量字符串段。
3. **mov指令**：汇编基础指令，`mov byte ptr [addr], data`表示将单字节数据写入指定内存地址。
4. **ASCII码**：字符的数字编码，十六进制形式`66h`对应十进制102，对应字符`f`。
5. **CrackMe**：专门用来逆向练习的小程序，CTF逆向题目大多属于CrackMe。

## 总结
这是逆向入门基础题，考察新手是否会切换汇编视图查找数据。很多初学者只习惯看F5伪代码，当字符串不在常量区时就找不到flag。
做题经验：F5伪代码找不到目标数据时，一定要切到汇编代码，检查有没有逐字节赋值的操作。
