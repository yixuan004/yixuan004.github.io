---
title: 709.转换成小写字母-python
date: 2021-12-19 17:42:38
tags:
    - 字符串
    - EASY
categories:
	- LeetCode-python
---

709.转换成小写字母（简单）

题目大意：

给你一个字符串```s```，将该字符串中的大写字母转换成相同的小写字母，返回新的字符串。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/to-lower-case/)

给你一个字符串```s```，将该字符串中的大写字母转换成相同的小写字母，返回新的字符串。

示例1：
```
输入：s = "Hello"
输出："hello"
```

示例2：
```
输入：s = "here"
输出："here"
```

示例3：
```
输入：s = "LOVELY"
输出："lovely"
```

# 分析和解答

主要是python语言的转小写，```str.lower()```

```python
class Solution(object):
    def toLowerCase(self, s):
        """
        :type s: str
        :rtype: str
        """
        return s.lower()
```