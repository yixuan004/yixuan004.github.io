---
title: 438.找到字符串中所有字母异位词-python
date: 2021-12-12 11:18:10
tags:
    - 哈希表
    - 字符串
    - 滑动窗口
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

438.找到字符串中所有字母异位词（中等）

题目大意：给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

异位词 指由相同字母重排列形成的字符串（包括相同的字符串）。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

异位词 指由相同字母重排列形成的字符串（包括相同的字符串）。

示例一：
```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

示例二：
```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

提示：
- 1 <= s.length, p.length <= 3 * 104
- s 和 p 仅包含小写字母

# 分析和解答

这个题要构建一个“滑动窗口”来减少操作次数的感觉，就是说如果每i+=1向后移动一位的时候，如果都重新计数，那么将会时间复杂度非常高。所以在每次移动的时候，只需要把i-1位置的删除，再加入i+window_size-1位置处最新的字符即可，这样的话每次移动只用操作两下；

一开始设置一个和p长度相等的s之后开始滑动窗口，删除头并新增尾只需要操作两次了（注意做字典判断相等时候，要把字典值为0的删除掉），另外注意print语句也会占用时间；

另外这个题也可以使用colletions.Counter，Counter可以进行一个计数操作，而且最关键的是**Counter居然可以执行加减操作**，
```python
>>> from collections import Counter
>>> text1 = "aaabbbccc"
>>> text1_count = Counter(text1)
>>> text1_count
Counter({'a': 3, 'b': 3, 'c': 3})
>>> text1_count.get('b')
3
>>> type(text1_count)
<class 'collections.Counter'>
>>> text2 = "abcd"
>>> text2_count = Counter(text2)
>>> text2_count
Counter({'a': 1, 'b': 1, 'c': 1, 'd': 1})
>>> tmp = text1_count - text2_count
>>> tmp
Counter({'a': 2, 'b': 2, 'c': 2})
```

在上述思路分析后，主要记住新加入的位置是i+window_size-1就好了，代码编写思路还是比较简单，这里使用了Counter的方法，实际上用字典的方法也可以，而且说不定可以增加效率：

```python
class Solution(object):
    def findAnagrams(self, s, p):
    """
    :type s: str
    :type p: str
    :rtype: List[int]
    """
    rList = []
    window_size = len(p)
    compare_dict = collections.Counter(p)
    record_s_dict = None

    for i in range(len(s)-window_size+1):
        if record_s_dict is None:
            record_s_dict = collections.Counter(s[i: window_size])
        else:
            remove_head = collections.Counter(s[i-1])
            # print("remove_head: ", i, remove_head)
            add_tail = collections.Counter(s[i+window_size-1]) # 调试还是比较容易看出来这个错误的
            # print("add tail: ", i, add_tail)
            record_s_dict -= remove_head
            record_s_dict += add_tail
            # print("record_s_dict: ", i, record_s_dict)
        if record_s_dict == compare_dict:
            rList.append(i)
    
    return rList
```


