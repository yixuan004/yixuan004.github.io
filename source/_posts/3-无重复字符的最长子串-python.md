---
title: 3.无重复字符的最长子串-python
date: 2021-11-30 23:09:03
tags:
    - 哈希表
    - 字符串
    - 滑动窗口
    - MEDIUM
categories:
	- LeetCode-python
---

3.无重复字符的最长子串（中等）

题目大意：给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

示例1：
```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

示例2：
```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

示例3：
```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

示例4：
```
输入: s = ""
输出: 0
```

提示：

- 0 <= s.length <= 5 * 104
- s 由英文字母、数字、符号和空格组成

# 分析和解答

这个题要及时get到使用滑动窗口来解决这个问题，因为题目要求的是”子串“，这是一个连续的

在get到是用滑动窗口解决问题后，滑动窗口本身来讲感觉就是一个队列，这个题是变长的


**整体上的思路是：**

遍历字符串，如果遇到不在滑动窗口（队列）中的字符，就加入队列的右边。如果遇到出现在队列中的字符，则队列的左边要缩到这个字符出现的下一位（感觉也可以用```find(xxx) + 1```来优化），然后再把这个字符加入进去；

在每次遍历i的时候，判断当前变长滑动窗口的大小和当前的最大值，来更新最大值，用来最终返回

解题：
```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """ 滑动窗口对应的数据结构是queue """
        if len(s) == 0:
            return 0
        que = []
        max_len = 0
        for i in range(len(s)):
            while s[i] in que:
                que = que[1:] # 
            que.append(s[i])
            max_len = max(max_len, len(que))
        
        return max_len
```