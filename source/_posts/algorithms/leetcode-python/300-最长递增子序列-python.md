---
title: 300.最长递增子序列-python
date: 2021-12-01 00:05:54
tags:
    - 数组
    - 二分查找
    - 动态规划
    - MEDIUM
    - TODO优化
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

300.最长递增子序列（中等）

题目大意：给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/longest-increasing-subsequence/)


给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

示例1：
```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

示例2：
```
输入：nums = [0,1,0,3,2,3]
输出：4
```

示例3：
```
输入：nums = [7,7,7,7,7,7,7]
输出：1
```

提示：
- 1 <= nums.length <= 2500
- 104 <= nums[i] <= 104

# 分析和解答

## 解法一：O(n^2)向前查找法

O(n^2)的解法还是比较好想的（简单题），先创建一个一维dp数组，然后走到每个位置的时候要往前看，看看前边比当前数字小的那些数组位置处，选择一个这些位置里dp值最大的，+1得到现在这个位置的dp值。最后返回max(dp)就可以

解题：
```python
class Solution(object):
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 思路还是很简单的，设置dp数组，走到每个位置后，【往前找】比这个位置小的元素，然后选一个最大的dp[i] + 1当做当前位置的dp
        if len(nums) == 0:
            return 0
        dp = [1 for i in range(len(nums))]
        for i in range(1, len(nums)):
            for j in range(i, -1, -1):
                if nums[i] > nums[j]:
                    dp[i] = max(dp[i], dp[j] + 1)
        return max(dp)
```


## 解法二：优化解法（二分查找）待补充