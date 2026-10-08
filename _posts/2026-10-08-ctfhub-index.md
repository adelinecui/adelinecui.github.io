---
layout: post
title: "CTFHub 信息泄露 - leakage"
date: 2026-10-08
categories: ctfhub
tags: [ctfhub,web,git泄露,源码泄露]
permalink: /ctfhub/git-leakage.html
---

## 一、题目信息
- 题目平台：CTFHub
- 题目类型：Web
- 题目名称：git leakage
- 考察知识点：Git源码泄露，git commit历史读取

## 二、解题环境
- 工具：Python脚本
- 解题思路：网站存在`.git`目录外网可访问漏洞，flag在历史提交中，当前页面已经删除flag，需要遍历commit历史找到add flag提交并读取flag.txt。

## 三、详细解题步骤
1. 访问靶机链接，探测发现网站根目录存在可访问的`.git`目录，存在典型Git源码泄露漏洞。
2. 读取仓库HEAD，递归遍历全部commit提交记录，找到备注为`add flag`的提交，记录该commit哈希，同时获取其父提交（init初始化提交）。
3. 获取两次commit对应的tree对象，分别解析两个tree包含的文件列表。
4. 对比两个tree的文件集合，定位`add flag`提交中新增的`flag.txt`文件。
5. 请求flag.txt对应的blob对象，解压读取文件内容，得到完整flag。

## 四、漏洞原理
网站部署上线时没有限制访问`.git`文件夹，git版本仓库直接暴露在公网。攻击者可以获取全部提交历史，恢复已经删除的文件与敏感信息。

## 五、修复建议
1. 网站部署时禁止对外访问`.git`目录；
2. 敏感信息不要提交进git仓库。

## Flag
`ctfhub{d2b8a02fa15c57091ba8ed2f}`
