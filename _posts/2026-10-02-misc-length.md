---
layout: post
title: "CTFHub Misc - Length"
date: 2026-10-02
categories: ctfhub
tags: [ctfhub, misc,流量分析,ICMP,ping]
permalink: /ctfhub/misc-length.html
---

# CTFHub Misc - 流量分析 Length
## 一、题目信息
平台：CTFHub
分类：Misc - 流量分析
题目名称：Length
题目提示：ping包的大小有些奇怪
难度：简单

## 二、考点
1. ICMP协议ping数据包流量分析
2. 区分ICMP请求包(type=8)与应答包(type=0)
3. 将数据包Length字段数值当作ASCII码，转换为字符拼接flag

## 三、原理分析
ping通信使用ICMP协议，分为请求包和应答包。
这道题将flag的每一个字符对应的ASCII码，藏在**ICMP请求包**的Length字段里。
> ⚠️重要坑点：应答包是服务器返回的包，不能读取它的Length。如果混入应答包的长度，解码结果完全错误，提交flag会提示错误。

## 四、解题过程
1. 下载题目附件`length.pcap`，使用Wireshark或者在线PCAP查看工具打开抓包文件。
2. 在Wireshark过滤框输入过滤规则：`icmp.type == 8`，只保留ping的ICMP请求数据包。
3. 从上到下，依次记录每一条请求包的`Length`的数字，按顺序全部记录。
4. 将记录好的每一个数字，转换成对应的ASCII字符，按先后顺序拼接起来。
5. 拼接完成后，得到完整flag，格式为`ctfhub{xxxxxx}`，提交验证。

## 五、总结
1. 题目提示ping包大小奇怪，引导我们重点观察数据包Length长度字段。
2. 最容易踩坑的地方：不能同时读取请求包+应答包，**只取type=8的请求包**。
3. 本题靶机每次启动环境，flag会动态变化，不能直接使用网上现成的固定flag。

## 六、flag
ctfhub{acb659f023}
