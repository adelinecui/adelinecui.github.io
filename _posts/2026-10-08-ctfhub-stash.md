---
layout: post
title: "CTFHub 信息泄露 - stash"
date: 2026-10-08
categories: ctfhub
tags: [ctfhub,Web,git泄露,源码泄露]
permalink: /ctfhub/git-stash-leak.html
---

## 一、题目信息
- 题目平台：CTFHub
- 题目类型：Web
- 题目名称：git泄露stash
- 考察知识点：**Git源码泄露、git stash暂存区利用**

## 二、解题环境
- 工具：GitHack源码泄露利用脚本
- 解题思路：网站存在.git目录外网可访问漏洞，开发者将flag通过git stash暂存未提交，通过工具下载完整git仓库，读取stash暂存内容获取flag。

## 三、详细解题步骤
1. 访问靶机链接，探测发现网站根目录存在可访问的`.git`目录，存在典型Git源码泄露漏洞。
2. 使用GitHack工具，批量下载靶机完整git仓库所有文件到本地。
3. 进入下载完成的git仓库目录，该目录包含完整的.git配置文件，为有效git仓库。
4. 查看stash暂存记录，发现存在未提交的stash缓存文件。
5. 查看暂存文件详细内容，读取得到完整flag。
6. 将获取的flag按格式提交，题目通关。

## 四、做题遇到的问题与解决
1. **问题1：仅下载单个stash文件，无法读取flag**
   - 原因：单独的stash文件仅保存哈希索引，无完整文件内容
   - 解决：必须下载整套完整.git仓库，才能解析还原暂存文件
2. **问题2：切换目录提示系统找不到路径**
   - 原因：盘符与文件夹名称输入错误，未确认本地解压目录名称
   - 解决：列出当前目录，确认文件夹名称再进入
3. **问题3：提示不是git仓库 fatal: not a git repository**
   - 原因：没有进入包含`.git`文件夹的根目录
   - 解决：确认当前目录存在`.git`文件夹，再执行git相关操作

## 五、题目总结
这是一道Web基础源码泄露题，核心考点：**Git源码泄露 + git stash暂存区读取**。
开发上线时没有删除服务器上的`.git`文件夹，造成仓库文件对外可下载。flag保存在stash暂存中，不会出现在git提交日志，容易被忽略。
解题要点：遇到.git泄露，除查看提交记录外，一定要检查stash暂存区。
## 六、flag
ctfhub{8d9caaf9b277e96101e93c35}
