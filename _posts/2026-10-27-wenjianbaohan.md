---
layout: post
title: "CTFHub RCE - 文件包含"
date: 2026-10-27
categories: ctfhub
tags: [ctfhub,web,文件包含,include,strpos,bypass]
permalink: /ctfhub/file-include-basic.html
---

# CTFHub RCE - 文件包含

## 一、题目信息
平台：CTFHub
分类：Web - 文件包含
题目名称：文件包含
难度：简单
Flag：`ctfhub{41390d3c17b6110a7bde7881}`

## 二、考点
1. PHP include 文件包含漏洞
2. strpos函数关键词过滤，仅针对单个参数校验
3. 文本格式一句话木马利用，include引入txt文件执行PHP代码
4. 参数隔离绕过：过滤只限制file参数，新增参数不受过滤控制

## 三、题目内容
页面接收GET参数file，后端使用strpos检测file参数内容，如果file中包含flag字符串，则拦截输出Hacker；如果不包含flag，就直接include包含file指定的文件。页面提示服务器存在shell.txt，文件内为一句话木马，可以被文件包含引入执行。

## 四、解题步骤
1. 访问题目靶场地址，页面展示PHP源码，确认过滤规则只检测file参数是否含有flag字符串。
2. 构造payload `?file=shell.txt`，shell.txt文件名不含flag，成功绕过strpos过滤，include引入木马文件。
3. 追加ctfhub参数给一句话木马，传入highlight_file读取flag文件，完整payload：
`http://靶场地址/?file=shell.txt&ctfhub=highlight_file('/flag');`
4. 页面返回flag内容，拿到flag。

## 五、知识点解析
1. include函数：PHP文件包含函数，引入文件后，文件内的PHP代码会被服务器解析执行，不受文件后缀限制。
2. strpos过滤缺陷：本题仅校验`$_GET["file"]`这一个参数，其他GET参数没有加入过滤逻辑，存在过滤不全面的问题。
3. 一句话木马原理：`eval($_REQUEST['ctfhub'])`接收ctfhub参数，将参数内容当作PHP代码执行。该参数属于木马内部接收，不会经过题目第一层的flag关键词检测。
4. 流程分层理解：第一层是题目源码的文件包含+参数过滤；第二层是被包含进来的木马独立接收参数执行代码，两层参数相互隔离。

## 六、总结
本题属于基础文件包含漏洞，核心缺陷是过滤只针对file参数，没有全局过滤所有用户输入。利用不含关键词的shell.txt绕过第一层检测，引入一句话木马，再通过木马额外参数执行读取文件代码获取flag。
安全防御上，需要对所有可控输入统一过滤，不单独只校验某一个参数，同时禁止包含用户可控的文件路径。
