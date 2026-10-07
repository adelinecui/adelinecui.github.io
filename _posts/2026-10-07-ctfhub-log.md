---
layout: post
title: "CTFHub 信息泄露 - Log"
date: 2026-10-07
categories: ctfhub
tags: [ctfhub,信息泄露,git泄露,源码泄露]
permalink: /ctfhub/git-leak-log.html
---

# CTFHub 信息泄露 - Git泄露 Log
## 一、题目信息
平台：CTFHub
分类：Web - 信息泄露
题目名称：Git泄露 Log
难度：简单

## 二、考点
1. Git仓库源码泄露漏洞
2. Nginx返回403仅禁止目录浏览，单个.git目录下文件仍可直接访问
3. Git版本历史：可查看历史提交记录，回退到旧提交，恢复已经删除的文件

## 三、原理分析
`.git`文件夹是Git版本控制仓库，保存项目全部历史提交记录。
本题Web服务器存在.git目录泄露，虽然无法直接浏览目录（403），但可以直接访问目录内的对象文件。
在Git的提交日志中可以看到多次提交，其中`add flag`提交存在flag文件，后续`remove flag`提交把flag删除。我们只需要查看add flag这次提交，就能拿到被删除的flag。

## 四、解题过程
1. 开启靶机，访问靶机地址，发现存在`.git`目录泄露。
2. 使用git-dumper工具，下载完整的远程git仓库到本地。
python -m git_dumper http://靶机地址/.git/ mygit

## 五、Flag
ctfhub{1d4564b1b0e0392c0007662e}
