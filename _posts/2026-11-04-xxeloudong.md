---
layout: post
title: "Pikachu - XXE 漏洞"
date: 2026-11-04
categories: pikachu
tags: [pikachu, web, xxe, xml外部实体注入]
permalink: /pikachu/xxe.html
---

## 一、题目信息
- 平台：Pikachu
- 分类：XXE 漏洞
- 难度：中等
- 通关标识：构造恶意的 XML 实体，成功读取服务器本地系统配置文件 `C:\Windows\win.ini`。

## 二、大白话原理讲解
后端代码用了解析 XML 的函数，但没有禁用外部实体。我们在 XML 里声明一个实体（相当于贴个标签），告诉服务器：“这个标签里装的是系统里的某个文件”。
当 XML 解析器读取到该标签时，就会乖乖去把那个文件的内容读出来并显示给我们看。这就叫“XML 外部实体注入”。

## 三、解题步骤

**1. 确认输入点**
页面提示“这是一个接收xml数据的api”，说明后端使用 XML 解析器处理我们提交的数据。

**2. 构造 XXE Payload**
在输入框中输入以下 Payload，利用 `<!ENTITY>` 声明外部实体，通过 `SYSTEM` 协议和 `file://` 伪协议读取系统文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///C:/Windows/win.ini">
]>
<foo>&xxe;</foo>
```
*(注：如果是 Linux 靶场，可以将路径替换为 `file:///etc/passwd`)*

**3. 验证结果**
点击“提交”，页面成功回显了 `win.ini` 文件的内容（包含 `[fonts]`、`[extensions]` 等配置项），证明存在 XXE 漏洞，且能够读取服务器本地敏感文件。

## 四、知识点解析
1. **XXE 漏洞**：全称 XML 外部实体注入（XML External Entity Injection）。当应用允许用户输入 XML 数据，且没有禁用外部实体时，攻击者就可以利用 `SYSTEM` 关键字读取本地文件、发起 SSRF 攻击甚至 RCE。
2. **DTD 实体声明**：`<!DOCTYPE>` 用于定义文档类型，`<!ENTITY>` 用于定义实体。`&xxe;` 用于引用实体，触发解析。

## 五、总结与防御
**本题总结**：
XXE 漏洞危害极大，不仅可读取文件，还可配合内网探测，是实战中非常重要的一类漏洞。

**安全防御建议**：
- **禁用外部实体**：在解析 XML 时，务必关闭外部实体解析（如在 PHP 中设置 `libxml_disable_entity_loader(true)`）。
- **使用安全的解析器**：使用 JSON 替代 XML 进行数据传输。
- **输入校验**：过滤 XML 中的 `<!DOCTYPE` 和 `<!ENTITY` 关键字。
