---
title: 136.只出现一次的数字-python
date: 2022-01-17 11:26:58
tags:
    - 位运算
    - 数组
    - EASY
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

136.只出现一次的数字（简单）

题目大意：

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明**：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？


<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/single-number/)

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明**：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

示例1：
```
输入: [2,2,1]
输出: 1
```

示例2：
```
输入: [4,1,2,1,2]
输出: 4
```

# 题目分析和解答

简单的位运算题目，因为题目说了其余每个元素都出现了两次，所以用xor就行了，真值表上异是1，同是0

```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        return_num = nums[0]
        for i in range(1, len(nums)):
            return_num ^= nums[i]  # 异或
        
        return return_num
```