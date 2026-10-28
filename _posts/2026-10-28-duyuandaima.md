---
layout: post
title: "CTFHub 文件包含 - 读取源代码"
date: 2026-10-28
categories: ctfhub
tags: [ctfhub,web,文件包含,php伪协议,php://filter,substr]
permalink: /ctfhub/file-include-read-source.html
---

# CTFHub 文件包含 - 读取源代码

## 一、题目信息
平台：CTFHub
分类：Web - 文件包含
题目名称：读取源代码
难度：简单
Flag：`ctfhub{a35fcef325d612fc1354f367}`

## 二、考点
1. PHP include 文件包含漏洞
2. substr 函数白名单校验与 php:// 伪协议利用
3. php://filter 伪协议读取本地任意文件
4. php://filter 资源流的 Base64 编码转换

## 三、题目内容
页面接收GET参数file，后端使用substr函数截取file参数的前6个字符，强制要求等于"php://"，如果不等于则输出"Hacker!!!"。通过校验后，使用include直接包含该参数指定的路径。页面底部提示"i don't have shell, how to get flag? flag in /flag"，暗示需要利用 PHP 伪协议直接读取服务器根目录下的 flag 文件。

## 四、解题步骤
1. 访问题目靶场地址，页面展示PHP源码，确认过滤规则强制要求file参数以"php://"开头。
2. 分析源码发现，`substr($_GET["file"], 0, 6)` 仅限制了前6个字符，这意味着我们可以使用任何以 `php://` 开头的伪协议。
3. 构造 `php://filter` 伪协议 payload，利用其 `resource` 流读取本地文件，完整payload构造如下：
`GET /?file=php://filter/resource=/flag`
4. 访问该链接，服务器成功读取并回显 `/flag` 文件内容，页面直接显示出 flag。
5. （备选方案）如果页面因为特殊字符未直接回显，可以使用 Base64 编码流，完整payload构造如下：
`GET /?file=php://filter/read=convert.base64-encode/resource=/flag`
6. 将页面返回的 Base64 字符串进行解码，即可获得明文 flag。

## 五、知识点解析
1. include函数与伪协议：当include包含的路径用户可控时，攻击者可以传入PHP支持的伪协议。`php://filter` 是一种元封装器，用于在读取文件时对数据流进行过滤或转换。
2. substr过滤缺陷：本题使用`substr($_GET["file"], 0, 6) === "php://"`进行强类型比较。虽然限制了前缀，但恰好把攻击范围引导到了PHP内置的各类`php://`伪协议上。`php://filter` 不需要开启 `allow_url_include`，只要 include 包含即可直接读取文件内容。
3. resource 与 stream：`php://filter/resource=/flag` 告诉 PHP 内核把 `/flag` 当作一个普通的数据流读出来，而不会去把它当作 PHP 代码解析，因此能原封不动地看到文件源码或敏感信息。

## 六、总结
本题属于基础文件包含结合伪协议的利用。核心在于看懂substr函数的白名单限制，顺水推舟利用`php://filter`绕过包含限制，读取服务器本地敏感文件。
安全防御上，应避免将用户可控输入直接传入include等包含函数；如果业务必须使用参数，应使用绝对路径映射（如Switch/Case分发），严禁用户直接控制路径；同时在php.ini中应严格配置`allow_url_include = Off`和`allow_url_fopen = Off`，防范伪协议攻击。
