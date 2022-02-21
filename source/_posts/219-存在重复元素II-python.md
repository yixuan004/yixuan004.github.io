---
title: 219.存在重复元素II-python
date: 2022-01-20 00:20:28
tags:
    - 数组
    - 哈希表
    - 滑动窗口
    - EASY
categories:
	- LeetCode-python
---

219.存在重复元素II（简单）

题目大意：

给你一个整数数组```nums```和一个整数```k```，判断数组中是否存在两个**不同的索引**```i```和```j```，满足```nums[i] == nums[j]```且```abs(i - j) <= k```。如果存在，返回```true```；否则，返回```false```。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/contains-duplicate-ii/)

题目大意：

给你一个整数数组```nums```和一个整数```k```，判断数组中是否存在两个**不同的索引**```i```和```j```，满足```nums[i] == nums[j]```且```abs(i - j) <= k```。如果存在，返回```true```；否则，返回```false```。

示例1：
```
输入：nums = [1,2,3,1], k = 3
输出：true
```

示例2：
```
输入：nums = [1,0,1,1], k = 1
输出：true
```

示例3：
```
输入：nums = [1,2,3,1,2,3], k = 2
输出：false
```

提示：

- 1 <= nums.length <= 105
- -109 <= nums[i] <= 109
- 0 <= k <= 105


# 分析和解答

用一个字典判断就行，这样每次更新最后出现的状态，O(n)时间复杂度

```python
class Solution(object):
    def containsNearbyDuplicate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        temp_dict = {}
        for i in range(len(nums)):
            if temp_dict.get(nums[i]) is not None:
                if i - temp_dict.get(nums[i]) <= k:
                    return True
            temp_dict[nums[i]] = i  # 每次强制更新为最后的，这样方便找最近的
        
        return False
```