---
title: 49.字母异位词分组-python
date: 2022-01-05 21:51:58
tags:
    - 哈希表
    - 字符串
    - 排序
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

49.字母异位词分组（中等）

题目大意：

给你一个字符串数组，请你将**字母异位词**组合在一起。可以按任意顺序返回结果列表。

**字母异位词**是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

# 题目

[题目链接](https://leetcode-cn.com/problems/group-anagrams/)

给你一个字符串数组，请你将**字母异位词**组合在一起。可以按任意顺序返回结果列表。

**字母异位词**是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

示例1：
```
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

示例2：
```
输入: strs = [""]
输出: [[""]]
```

示例3：
```
输入: strs = ["a"]
输出: [["a"]]
```

提示：
- ```1 <= strs.length <= 104```
- ```0 <= strs[i].length <= 100```
- ```strs[i]```仅包含小写字母

# 分析和解答

这个题想想自己做法也挺奇葩的，只能说Counter永远的神了

先重整理数组，在原有的字符串基础上变成```[字符串, Counter对象, 标记位（标记是否已经加入了）]```

一个两重for循环，在外层for循环中，如果没有加入则加入，如果已经加入了则continue。在内层循环中，a-b, b-a判断Counter是否相等（这里比如a是外层的i，b是内层的j），如果相等并且没加入，就加入到外层循环初始化的list中，然后内层循环结束后append


**Counter永远的神！（除了效率hhh）**

```python
class Solution(object):
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """
        
        from collections import Counter
        
        # 一旦用上Coutner耗时就会很高？ 不过Counter真的是个好东西
        temp_list = []
        for i, item in enumerate(strs):
            temp_list.append([item, Counter(item), 0])  # 0是一个标记位，标记是否被加进去了
        
        return_list = []
        for i in range(len(temp_list)):
            if temp_list[i][2] == 1:
                continue
            add_list = [temp_list[i][0]]
            temp_list[i][2] = 1
            for j in range(i+1, len(temp_list)):
                if temp_list[j][1] == temp_list[i][1] and temp_list[j][2] == 0:
                    add_list.append(temp_list[j][0])
                    temp_list[j][2] = 1
            return_list.append(add_list)
            add_list = []
    
        return return_list
```