---
title: 1446.连续字符-python
date: 2021-12-13 22:35:08
tags:
    - 字符串
    - EASY
categories:
	- LeetCode-python
---

1446.连续字符（简单）

题目大意：给你一个字符串 s ，字符串的「能量」定义为：只包含一种字符的最长非空子字符串的长度。请你返回字符串的能量。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/consecutive-characters/)

题目大意：给你一个字符串 s ，字符串的「能量」定义为：只包含一种字符的最长非空子字符串的长度。请你返回字符串的能量。

示例1：
```
输入：s = "leetcode"
输出：2
解释：子字符串 "ee" 长度为 2 ，只包含字符 'e' 。
```

示例2：
```
输入：s = "abbcccddddeeeeedcba"
输出：5
解释：子字符串 "eeeee" 长度为 5 ，只包含字符 'e' 。
```

示例3：
```
输入：s = "triplepillooooow"
输出：5
```

示例4：
```
输入：s = "hooraaaaaaaaaaay"
输出：11
```

示例5：
```
输入：s = "tourist"
输出：1
```

提示：
- 1 <= s.length <= 500
- s 只包含小写英文字母。

# 分析和解答

比较简单，O(n)遍历一次，如果不等清空重新计数就可以了，另外感觉有很多种实现方法。

```python
class Solution(object):
    def maxPower(self, s):
        """
        :type s: str
        :rtype: int
        """
        if len(s) == 0:
            return 0

        max_cnt = 1
        cnt = 1
        for i in range(1, len(s)):
            if s[i] == s[i-1]:
                cnt += 1
            else:
                cnt = 1
            max_cnt = max(max_cnt, cnt)

        return max_cnt
```