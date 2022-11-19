---
title: 423.从英文中重建数字-python
date: 2021-11-29 23:48:17
tags:
    - 哈希表
    - 数学
    - 字符串
    - MEDIUM
categories:
	- LeetCode-python
---

423.从英文中重建数字（中等）

题目大意：给你一个字符串 s ，其中包含字母顺序打乱的用英文单词表示的若干数字（0-9）。按 升序 返回原始的数字。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/reconstruct-original-digits-from-english/)

给你一个字符串 s ，其中包含字母顺序打乱的用英文单词表示的若干数字（0-9）。按 升序 返回原始的数字。

示例1：
```python
输入：s = "owoztneoer"
输出："012"
```

示例2：
```python
输入：s = "fviefuro"
输出："45"
```

提示：

- 1 <= s.length <= 105
- s[i] 为 ["e","g","f","i","h","o","n","s","r","u","t","w","v","x","z"] 这些字符之一
- s 保证是一个符合题目要求的字符串


# 分析和解答

这种题有一种脑筋急转弯的感觉，每个数字的英文中会有一些特点，这样可以统计出来每个英文字母在哪些数字中出现了，如下图所示。这样可以通过计数的方式来从“出现一次”的分析，例如“x”只在six中出现，之后可以像数独一样链着分析。

统计分析规律，列出每个字母在哪些数字的英文中出现了，然后有些字母实际上只在一个数字中出现了，统计这些字母的个数就是这个数字的个数。然后来有些字母在两个数字中出现的，贪心下

**需要注意，尽量避免使用一个字母在一个单词中出现两次的，例如nine中的n**

```
zero
one
two
three 
four
five
six
seven
eight
nine
```

![](/images/2021-11-30-00-12-10.png)



解题：
```python
class Solution(object):
    def originalDigits(self, s):
        cnt_dict = {}
        m = [0 for i in range(10)]
        for i, c in enumerate(s):
            if cnt_dict.get(c) is None:
                cnt_dict[c] = 1
            else:
                cnt_dict[c] += 1
        # 单位的
        m[8] = cnt_dict.get('g', 0)
        m[4] = cnt_dict.get('u', 0)
        m[2] = cnt_dict.get('w', 0)
        m[6] = cnt_dict.get('x', 0)
        m[0] = cnt_dict.get('z', 0)

        # 两位的
        m[5] = cnt_dict.get('f', 0) - m[4]
        m[3] = cnt_dict.get('h', 0) - m[8]
        m[7] = cnt_dict.get('v', 0) - m[5]

        # 差0 1 和 9
        m[0] = cnt_dict.get('r', 0) - m[3] - m[4]
        m[1] = cnt_dict.get('o', 0) - m[0] - m[2] - m[4]
        # m[9] = cnt_dict.get('n', 0) - m[1] - m[7] # 字母尽量选择出现1次的，所以n不行
        m[9] = cnt_dict.get('i', 0) - m[5] - m[6] - m[8]

        # 可否优化
        return "".join(str(i) * m[i] for i in range(10))
```