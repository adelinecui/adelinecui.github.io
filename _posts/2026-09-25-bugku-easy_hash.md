---
layout: post
title: Bugku Crypto: easy_hash
categories: bugku
tags: [Crypto, MD5, Python]
---
# Bugku easy_hash
```python
"""
题目信息
平台：Bugku CTF
类型：Crypto
附件：归档.zip，内含 flag.py 和 output

题目描述
题目给了加密脚本flag.py和加密结果文件output，阅读代码分析加密逻辑，还原出flag。

思路分析
阅读flag.py源码：
加密逻辑：
1. 读取flag文本内容，把字符串拆分为单个独立字符
2. 对每一个字符单独做MD5哈希运算
3. 将每个字符对应的MD5结果，一行一条写入output

解密思路：
读取output中的每一行MD5，遍历可能字符，反向匹配MD5得到原始单个字符；
按照顺序拼接所有字符，得到完整flag。
可用在线Python平台运行解密脚本，或者使用cmd5网站逐个查表解密。
"""

# 加密脚本 flag.py
'''
import hashlib
from multiprocessing import Pool

def compute_md5(char):
    md5_flag = hashlib.md5(char.encode())
    return md5_flag.hexdigest()

if __name__ == '__main__':
    with open('flag','r') as flag_file:
        content = flag_file.read()
        chars = list(content)
    with Pool() as pool:
        md5_results = pool.map(compute_md5, chars)
    with open('output','w') as output_file:
        for result in md5_results:
            output_file.write(result + '\n')
'''

# 解密脚本
import hashlib

def get_char(hash_str):
    chars = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ{}_"
    for c in chars:
        if hashlib.md5(c.encode()).hexdigest() == hash_str.strip():
            return c
    return "?"

md5_text = """
# output...
"""

res = ""
lines = md5_text.splitlines()
for line in lines:
    ch = get_char(line)
    res += ch
print("flag =", res)

"""
Flag
flag{We1c0me_t0_the_w0r1d_0f_md5}

总结
本题考察MD5哈希基础。不是对整段flag一次性MD5加密，而是逐个字符分别哈希，是这道题的关键点。
解密时只需要对每一条哈希反向查表，再按顺序拼接字符即可拿到明文。
"""
