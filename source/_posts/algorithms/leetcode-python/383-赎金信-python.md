---
title: 383.赎金信-python
date: 2021-12-14 14:06:29
tags:
    - 哈希表
    - 字符串
    - 计数
    - EASY
categories:
	- LeetCode-python
---

383.赎金信（简单）

题目大意：

给你两个字符串：ransomNote 和 magazine ，判断 ransomNote 能不能由 magazine 里面的字符构成。

如果可以，返回 true ；否则返回 false 。

magazine 中的每个字符只能在 ransomNote 中使用一次。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/ransom-note/)

给你两个字符串：ransomNote 和 magazine ，判断 ransomNote 能不能由 magazine 里面的字符构成。

如果可以，返回 true ；否则返回 false 。

magazine 中的每个字符只能在 ransomNote 中使用一次。

示例1：
```
输入：ransomNote = "a", magazine = "b"
输出：false
```

示例2：
```
输入：ransomNote = "aa", magazine = "ab"
输出：false
```

示例3：
```
输入：ransomNote = "aa", magazine = "aab"
输出：true
```

提示：

- 1 <= ransomNote.length, magazine.length <= 105
- ransomNote 和 magazine 由小写英文字母组成

# 分析和解答

python中Counter的妙用，Counter是可以相减的！

所以说如果在ransomNote中，减掉magazine后是空的，就代表可以组成的

```python
class Solution(object):
    def canConstruct(self, ransomNote, magazine):
        """
        :type ransomNote: str
        :type magazine: str
        :rtype: bool
        """
        c1 = Counter(ransomNote)
        c2 = Counter(magazine)
        return c1 - c2 == Counter("")
```