---
title: 28.实现strStr-python
date: 2021-12-03 11:14:37
tags:
    - 双指针
    - 字符串
    - 字符串匹配
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---


28.实现strStr()（中等）

题目大意：实现 strStr() 函数。给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/implement-strstr/)

实现 strStr() 函数。

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。


说明：
```
当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 strstr() 以及 Java 的 indexOf() 定义相符。
```

示例1：
```
输入：haystack = "hello", needle = "ll"
输出：2
```

示例2：
```
输入：haystack = "aaaaa", needle = "bba"
输出：-1
```

示例3：
```
输入：haystack = "", needle = ""
输出：0
```

提示：
- 0 <= haystack.length, needle.length <= 5 * 104
- haystack 和 needle 仅由小写英文字符组成

# 分析和解答

这个题就是KMP算法的完全模板，虽然题目标注的难度是简单但感觉那是调用函数直接的做法。。。例如在python中可以直接用str1.find(str2)来解决，而题目要考察的就是这个函数的实现

KMP算法非常巧妙，这里学习的是Youtube+b站转载翻译的一个思路：
> https://www.bilibili.com/video/BV18k4y1m7Ar?p=1

思路是首先在needle（又称pattern）上构建pre数组，或者有人称为next数组。next数组的含义很有可能在面试中被问到，自己写几个的话，感觉强行理解这个next数组就是当前这个位置下，pattern的首尾最长公共子串的数目，例如```abc ... abc```是一个首尾重复的，这样的话，j在回退的时候，不必每次都回退到头部，而是回退到next[j]的位置，复习一下这个构建next数组的操作，这个kmp算法感觉还是要做到经常操作的（暂时不太考虑很特殊的情况）：
```python
def construct_pre_arr(pattern):
    j = 0
    i = 1
    pre = [0] * len(pattern)
    while i < len(pattern):
        if pattern[i] == pattern[j]:
            pre[i] = j + 1
            j += 1
            i += 1
        elif pattern[i] != pattern[j]:
            if j == 0:
                pre[i] = 0
                i += 1
            else:
                j = next[j]    
    return pre
```

在构建好pre数组后，之后就是两个串来操作了，使用pre数组，让在串str1中寻找pattern的时候，不用每次都回退到pattern的头部，自己手工复习一下这个写法，整个写法上来看是先判断有没有（j是否遍历到了最后一位），再找是哪一段。这里基本只是运用pre数组了，比之前修改还简单些

```python
i = 0  # str1的index
j = 0  # pattern的index

while i < len(str1) and j < len(pattern):
    if str1[i] == pattern[j]:
        i += 1
        j += 1
    else:
        if j == 0:
            i += 1
        else:
            j = pre[j]

if j == len(pattern):
    return i - j
else:
    return -1
```


整体题目的解答：
```python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        m, n = len(haystack), len(needle)
       
        def compute_pre_array(needle):  # 这个是计算pattern串的pre数组
            pre = [0 for i in range(len(needle))]
            j = 0
            i = 1
            while i < len(needle):
                if needle[i] == needle[j]:
                    pre[i] = j + 1  # 注意这里的等于j + 1，实际上就是一个长度的感觉
                    j += 1
                    i += 1
                else:
                    if j == 0:
                        pre[i] = 0
                        i += 1
                    elif j != 0:
                        j = pre[j-1]  # 精髓
            return pre  # pre数组的含义是，单个串在这个位置上，前缀长度等于后缀长度的个数，但是为什么要这么做还不是很理解
        pre = compute_pre_array(needle)
       
        # step2 使用pre数组
        i = 0  # 串1的index
        j = 0  # 串2的index
        while i < m and j < n:
            if haystack[i] == needle[j]:
                i += 1
                j += 1
            else:
                if j == 0:
                    i += 1
                elif j != 0:
                    j = pre[j-1]
        if j == len(needle):
            return i - j # i停下的位置找回去
        else: 
            return -1
```