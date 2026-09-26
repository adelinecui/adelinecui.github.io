---
layout: post
title: Bugku telnet
categories: bugku
tags: [Misc,流量分析]
---
# Bugku telnet
## 题目信息
- 平台：Bugku CTF
- 类型：Misc 流量分析
- 题目链接：https://ctf.bugku.com/challenges/detail/id/72.html
- 附件：telnet.zip，解压得到 networking.pcap

## 题目描述
给一个pcap流量包，分析流量，找到flag。

## 思路分析
Telnet协议是明文传输协议，通信过程所有输入输出内容不加密，全部保存在TCP流量中。我们只需要找到Telnet对应的TCP会话，追踪TCP流，读取会话里的明文内容，就能拿到flag。

## 解题过程
### 方法1：Wireshark（或者网页版PCAP Viewer Online）分析
1. 用Wireshark打开`networking.pcap`流量文件。
2. 在显示过滤器输入：`tcp.port == 23`，过滤Telnet流量（Telnet默认端口23）。
3. 任意选中一条数据包，右键选择【追踪】→【TCP流】。
4. 在弹出的TCP流窗口查看完整交互明文，找到flag。

> 网页版PCAP Viewer Online操作：打开网站上传pcap，过滤器输入`tcp.port ==23`，选中数据包，Analyze -> Follow TCP Stream。网页版偶尔存在解析bug，可以换方法2。

### 方法2：简易快速解法
因为flag是明文字符串直接写在pcap文件内，可以直接用记事本打开`networking.pcap`，使用快捷键`Ctrl+F`，搜索关键词`flag`，直接定位flag。

## Flag
`flag{d316759c281bf925d600be698a4973d5}`

## 总结
Telnet没有加密机制，所有交互数据明文传输。这也是为什么现在服务器很少使用Telnet，改用加密的SSH协议，防止账号、信息被抓包窃取。
