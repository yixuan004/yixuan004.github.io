---
title: 2044.统计按位或能得到最大值的子集数目-python
date: 2022-03-15 14:53:56
tags:
    - 位运算
    - 数组
    - 回溯
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode每日一题
---

2044.统计按位或能得到最大值的子集数目（中等）

给你一个整数数组 ```nums``` ，请你找出 ```nums``` 子集 **按位或** 可能得到的 **最大值** ，并返回按位或能得到最大值的 **不同非空子集的数目** 。

如果数组 ```a``` 可以由数组 ```b``` 删除一些元素（或不删除）得到，则认为数组 ```a``` 是数组 ```b``` 的一个 **子集** 。如果选中的元素下标位置不一样，则认为两个子集 **不同** 。

对数组 ```a``` 执行 **按位或** ，结果等于 ```a[0] OR a[1] OR ... OR a[a.length - 1]```（下标从 **0** 开始）。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/count-number-of-maximum-bitwise-or-subsets/)

给你一个整数数组 ```nums``` ，请你找出 ```nums``` 子集 **按位或** 可能得到的 **最大值** ，并返回按位或能得到最大值的 **不同非空子集的数目** 。

如果数组 ```a``` 可以由数组 ```b``` 删除一些元素（或不删除）得到，则认为数组 ```a``` 是数组 ```b``` 的一个 **子集** 。如果选中的元素下标位置不一样，则认为两个子集 **不同** 。

对数组 ```a``` 执行 **按位或** ，结果等于 ```a[0] OR a[1] OR ... OR a[a.length - 1]```（下标从 **0** 开始）。

示例1：
```
输入：nums = [3,1]
输出：2
解释：子集按位或能得到的最大值是 3 。有 2 个子集按位或可以得到 3 ：
- [3]
- [3,1]
```

示例2：
```
输入：nums = [2,2,2]
输出：7
解释：[2,2,2] 的所有非空子集的按位或都可以得到 2 。总共有 23 - 1 = 7 个子集。
```

示例3：
```
输入：nums = [3,2,1,5]
输出：6
解释：子集按位或可能的最大值是 7 。有 6 个子集按位或可以得到 7 ：
- [3,5]
- [3,1,5]
- [3,2,5]
- [3,2,1,5]
- [2,5]
- [2,1,5]
```

提示：
- ```1 <= nums.length <= 16```
- ```1 <= nums[i] <= 10^5```

# 分析和解答

典型的 ```2^n``` 暴力遍历题目，题目里给的提示 ```16``` 也代表了这个意思， ```2^16 = 65536```；

如果是每日一题的话就先不用回溯搞了，回溯好像是dfs分为取和不取的两个分支往下dfs，写起来稍微有点麻烦；

大佬的提醒下不用做什么哈希表了，在迭代判断的过程中直接记录 ```max``` ，然后出现新的更大的 ```refresh``` 掉那个 ```cnt``` 、没出现新的然后等于 ```max``` 的直接 ```cnt+=1``` 这样就可以了。

```python
class Solution(object):
    def countMaxOrSubsets(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        max_or = 0
        max_or_count = 0
        for i in range(2**len(nums)):
            tmp = i
            tmp_list = []
            while tmp > 0:
                tmp_list.append(tmp&1)
                tmp >>= 1
            tmp_list += [0 for _ in range(len(nums)-len(tmp_list))]
            # print(tmp_list)  # 1是选择，0是不选择

            tmp_or = 0
            for j in range(len(tmp_list)):
                if tmp_list[j] == 1:
                    tmp_or |= nums[j]  # 按位或简单写法
            if tmp_or > max_or:
                max_or = tmp_or
                max_or_count = 1
            elif tmp_or == max_or:
                max_or_count += 1
        
        return max_or_count
```