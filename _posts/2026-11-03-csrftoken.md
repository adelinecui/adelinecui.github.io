---
layout: post
title: "Pikachu - CSRF csrf(token)"
date: 2026-11-02
categories: pikachu
tags: [pikachu, web, csrf, token重放, burpsuite]
permalink: /pikachu/csrf-token.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：CSRF -> csrf(token)
- 难度：中等
- 通关标识：利用抓包工具重放带有 Token 的请求，成功修改信息

## 二、考点
1. CSRF Token 的防御机制
2. Token 重放攻击（Replay Attack）
3. Burp Suite Repeater 模块的运用

## 三、题目内容
本关卡引入了 CSRF Token 机制。在提交修改个人信息的表单时，请求体中包含一个随机生成的 `token` 字段。服务器在校验请求时会验证该 Token。但该关卡存在逻辑缺陷，Token 并没有严格做到“一次性使用”，导致可以被重放。

## 四、解题步骤

**1. 抓取正常请求**
登录系统，进入个人信息修改页面。开启 Burp 拦截，修改信息并提交。抓取到的 POST 请求体如下：
```http
POST /pikachu/vul/csrf/csrftoken/csrf_token_edit.php HTTP/1.1
Host: 127.0.0.1
Content-Type: application/x-www-form-urlencoded

sex=boy&phonenum=111&add=chain&email=vince%40pikachu.com&token=2a4e1b9c...&submit=submit
```
可以看到，请求中包含了一个随机的 `token` 值。

**2. 发送至 Repeater 重放**
右键点击该请求，选择 `Send to Repeater`。在 Repeater 模块中：
- 第一次点击 `Send`，服务器返回“修改成功”。
- 将请求体中的 `phonenum` 修改为 `888888`，**保持 `token` 值不变**，再次点击 `Send`。

**3. 验证漏洞**
观察第二次的 Response，发现服务器依然接受了请求并提示“修改成功”。回到网页刷新个人信息，发现手机号被改成了 `888888`。这说明 Token 校验机制存在缺陷，未能有效防止重放攻击。

## 五、知识点解析
1. **CSRF Token 防御**：服务器在返回表单时生成一个随机的、不可预测的 Token（通常存入 Session）。提交时，服务器比对请求中的 Token 和 Session 中的 Token。这能有效阻止攻击者伪造请求，因为攻击者无法预测 Token 的值。
2. **Token 重放漏洞**：如果服务器只验证 Token 的存在和正确性，但在验证通过后**没有立即销毁该 Token**，或者允许 Token 在短时间内多次使用，攻击者就可以通过抓包重放的方式，反复利用同一个 Token 发起请求。

## 六、总结与防御
**本题总结**：
虽然有 Token 防护，但代码逻辑的不严谨导致了重放漏洞。Token 的核心在于“不可预测”和“一次性”。

**安全防御建议**：
- **一次性 Token**：每次表单提交后，无论成功与否，服务端必须立即销毁旧的 Token，并在下次页面加载时生成全新的 Token。
- **绑定 Token**：确保 Token 与当前用户的 Session 严格绑定，不能跨用户使用。
- **校验 Referer 和 Origin**：作为辅助防御手段，验证请求的来源域名是否合法。
