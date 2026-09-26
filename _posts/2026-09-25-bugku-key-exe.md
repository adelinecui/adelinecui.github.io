---
layout: post
title: Bugku Misc KEY.exe
categories: bugku
tags: [MISC, exe, base64, qrcode]
---
# Bugku KEY.exe

##题目信息

 平台：Bugku CTF
 题目：多种方法解决
 题型：MISC 文件隐写
 附件：file(1).zip，解压得到 KEY.exe

##题目描述

 给出可执行程序KEY.exe，寻找隐藏在程序内的flag。

##解题思路

 程序很小，没有复杂汇编代码，属于入门文件隐写题。直接修改后缀查看文本内容，发现里面嵌入了base64编码的图片。

##解题步骤：

 1. 下载附件压缩包并解压，得到 KEY.exe
 2. 复制一份KEY.exe，将副本后缀名改为txt，得到KEY.txt
 3. 用记事本打开KEY.txt，可以看到开头 `data:image/jpg;base64,`
 4. **只复制逗号后面**全部base64字符串，不要复制前面`data:image/jpg;base64,`
 5. 打开青少年CTF在线工具，选择【图片Base64转换】
 6. 将base64粘贴进输入框，点击【转换为图片】，页面生成二维码图片
 7. 扫码二维码，读取内容，拿到flag

##踩坑记录：

 1. 复制编码时，不能带上`data:image/jpg;base64,`前缀，否则转换失败。
 2. 不需要本地Python环境，使用在线工具直接解码出图片，操作简单。


##Flag

 KEY{dca57f966e4e4e31fd5b15417da63269}

##总结

 本题考察基础文件隐写。遇到小型exe杂项题，可以优先尝试修改后缀用记事本查看，不需要IDA、OD这类复杂逆向工具。文件内嵌入 base64图片，使用在线base64转图片工具即可解码得到二维码，扫码获取flag。
