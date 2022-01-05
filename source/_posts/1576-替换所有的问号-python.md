---
title: 1576.替换所有的问号-python
date: 2022-01-05 21:28:05
tags:
    - 字符串
    - EASY
categories:
	- LeetCode-python
---

1576.替换所有的问号（简单）

题目大意：

给你一个仅包含小写英文字母和```'?'```字符的字符串```s```，请你将所有的```'?'```转换为若干小写字母，使最终的字符串不包含任何**连续重复**的字符。

注意：你**不能**修改非```'?'```字符。

题目测试用例保证**除**```'?'```字符**之外**，不存在连续重复的字符。

在完成所有转换（可能无需转换）后返回最终的字符串。如果有多个解决方案，请返回其中任何一个。可以证明，在给定的约束条件下，答案总是存在的。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/replace-all-s-to-avoid-consecutive-repeating-characters/)

题目大意：

给你一个仅包含小写英文字母和```'?'```字符的字符串```s```，请你将所有的```'?'```转换为若干小写字母，使最终的字符串不包含任何**连续重复**的字符。

注意：你**不能**修改非```'?'```字符。

题目测试用例保证**除**```'?'```字符**之外**，不存在连续重复的字符。

在完成所有转换（可能无需转换）后返回最终的字符串。如果有多个解决方案，请返回其中任何一个。可以证明，在给定的约束条件下，答案总是存在的。

示例1：
```
输入：s = "?zs"
输出："azs"
解释：该示例共有 25 种解决方案，从 "azs" 到 "yzs" 都是符合题目要求的。只有 "z" 是无效的修改，因为字符串 "zzs" 中有连续重复的两个 'z' 。
```


示例2：
```
输入：s = "ubv?w"
输出："ubvaw"
解释：该示例共有 24 种解决方案，只有替换成 "v" 和 "w" 不符合题目要求。因为 "ubvvw" 和 "ubvww" 都包含连续重复的字符。
```

示例3：
```
输入：s = "j?qg??b"
输出："jaqgacb"
```

示例4：
```
输入：s = "??yw?ipkj?"
输出："acywaipkja"
```

提示：
- 1 <= s.length <= 100
- s 仅包含小写英文字母和 '?' 字符

# 分析和解答

每日一题还是挺简单的，这题主要注意的情况是一个字符最多相邻两个，也就是说只要准备三个需要被换的字符，比如```['a', 'b', 'c']```就可以了

另外是还需要判断问号连续出现的情况就行了

另：python的字符串处理最好先用列表，然后再join到一起，耗时问题

```python
class Solution(object):
    def modifyString(self, s):
        """
        :type s: str
        :rtype: str
        """

        # 注意可能连续出现两个问号！！！！

        if len(s) == 1 and s[0] == '?':
            return "a"


        letters = ['a', 'b', 'c']  # 必将在三个字母中找到答案！ 这个地方还是挺关键的
        return_list = []  # python字符串，先用list，然后join回去

        for i in range(len(s)):
            if s[i] != '?':
                return_list.append(s[i])
            else:
                if i == 0:
                    for letter in letters:
                        if letter != s[i+1]:
                            return_list.append(letter)
                            break
                elif i == len(s) - 1:
                    for letter in letters:
                        if letter != return_list[i-1]:
                            return_list.append(letter)
                            break
                else:
                    for letter in letters:
                        if letter != s[i+1] and letter != return_list[i-1]:
                            return_list.append(letter)
                            break

        return ''.join(return_list)
```