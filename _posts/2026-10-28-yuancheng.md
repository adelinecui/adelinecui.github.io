---
layout: post
title: "CTFHub 文件包含 - 远程包含"
date: 2026-10-28
categories: ctfhub
tags: [ctfhub,web,文件包含,远程包含,php伪协议,php://input,strpos]
permalink: /ctfhub/file-include-remote.html
---

# CTFHub 文件包含 - 远程包含

## 一、题目信息
平台：CTFHub
分类：Web - 文件包含
题目名称：远程包含
难度：简单
Flag：`ctfhub{ff5b386b7570bbb01cf07da2}`

## 二、考点
1. PHP include 文件包含漏洞（远程包含 RFI）
2. strpos 函数逻辑缺陷绕过
3. php://input 伪协议执行 POST 数据作为 PHP 代码
4. 浏览器 Console 发送 POST 请求技巧

## 三、题目内容
页面接收GET参数file，后端使用 `strpos` 函数检查参数中是否包含 "flag" 字符串。如果不包含，则 `include` 包含该参数指定的路径。页面底部提示"i don't have shell, how to get flag?"，并提供了一个指向 phpinfo.php 的链接，用于查看服务器配置。

## 四、解题步骤
1. 访问题目靶场地址，页面展示PHP源码，发现过滤逻辑 `!strpos($_GET["file"], "flag")`。该逻辑意为：如果参数中不包含 "flag"，则允许包含。这意味着我们可以使用任何不含 "flag" 字样的伪协议。
2. 访问页面提供的 `phpinfo.php` 链接，确认服务器环境。搜索 `allow_url_include`，确认其状态为 `On`，这为使用 `php://input` 伪协议提供了前提条件。
3. 按 `F12` 打开浏览器开发者工具，切换到 Console（控制台）标签页。
4. 构造并发送 POST 请求，将 `file` 参数设为 `php://input`，利用 POST Body 传入 PHP 系统命令，探测根目录文件。在 Console 中执行如下代码：
`fetch('/?file=php://input', { method: 'POST', body: '<?php system("ls /"); ?>' }).then(r => r.text()).then(t => console.log(t));`
5. 查看 Console 输出的结果，成功获取根目录列表，并在其中发现目标文件 `/flag`。
6. 修改 POST Body 中的代码，读取该文件内容。在 Console 中执行如下代码：
`fetch('/?file=php://input', { method: 'POST', body: '<?php system("cat /flag"); ?>' }).then(r => r.text()).then(t => console.log(t));`
7. 服务器执行命令并回显页面内容，在 Console 的输出结果中找到 Flag，提交即可。

## 五、知识点解析
1. **strpos 过滤缺陷**：`strpos` 函数用于查找字符串首次出现的位置，如果没找到则返回 `false`。本题中 `!strpos(...)` 相当于“如果不包含 flag 字符串”，这就给了我们使用 `php://input` 等不含 flag 关键词的 payload 绕过的机会。
2. **php://input 伪协议**：这是一个可以访问请求原始数据的只读流。当被 `include` 包含时，如果 `allow_url_include` 开启，其中的 POST 数据会被当作 PHP 代码解析执行，从而造成任意代码执行（RCE）。
3. **无工具利用**：在 CTF 中如果不方便下载工具（如 Burp Suite），可以直接利用浏览器的 `fetch` API 在 Console 中构造 POST 请求，方便快捷地进行漏洞验证。

## 六、总结
本题属于文件包含漏洞中经典的远程包含利用。核心在于识别 `strpos` 的过滤逻辑漏洞，并通过 `php://input` 伪协议配合 POST 请求绕过限制，直接执行系统命令读取目标文件。
安全防御上，应避免将用户可控输入直接传入 include 等包含函数；同时在 php.ini 中应严格配置 `allow_url_include = Off` 和 `allow_url_fopen = Off`，彻底禁用远程包含功能，防范伪协议攻击。
