---
layout: post
title: "CTFHub 流量分析 - LengthBinary"
date: 2026-10-02
categories: ctfhub
tags: [ctfhub,流量分析,ICMP,ping,隐写]
permalink: /ctfhub/lengthbinary.html
---

# CTFHub 流量分析 - LengthBinary
## 一、题目信息
平台：CTFHub
分类：流量分析
题目名称：LengthBinary
难度：简单
题目描述：ping 包的大小有些奇怪
附件：pcap抓包文件

## 二、考点
1. ICMP ping协议基础
2. 基于数据包长度的二进制隐写
3. 二进制转ASCII解码

## 三、原理分析
本题利用ICMP Echo Request（ping请求包）的数据包长度来传递二进制信息。
抓包里面只有两种长度的ping包：74 和 106。
约定规则：长度74代表二进制`0`，长度106代表二进制`1`。
按数据包出现先后顺序，收集0、1，每8个比特一组，转换成ASCII字符，最终拼接得到flag。

## 四、解题过程
1. 使用在线pcap查看工具打开抓包文件，设置过滤条件 `icmp.type == 8`，只保留ping请求包。
2. 按包的顺序，逐个记录Length字段的值：74记0，106记1，拼接完整二进制串。
3. 将二进制字符串按8bit分组，每组转为对应的ASCII字符。
4. 拼接所有字符，得到flag。

## 五、flag
`ctfhub{04efed1e05}`

## 六、总结
这是典型的流量隐写，数据不在包内的载荷，而是**数据包本身的属性（长度）**作为隐藏通道。遇到ping流量题目，除了看data字段，也要留意包长、ID、序列号这类字段。
