---
title: 15.三数之和-python
date: 2021-12-15 17:46:36
tags:
    - 数组
    - 双指针
    - 排序
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

15.三数之和（中等）

题目大意：

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/3sum/)

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

示例1：
```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

示例2：
```
输入：nums = []
输出：[]
```

示例3：
```
输入：nums = [0]
输出：[]
```

提示：
- 0 <= nums.length <= 3000
- -105 <= nums[i] <= 105

# 分析和解答

O(n^3)是一种最容易想到的解法，但是会超时。

外层i，内层双指针+移动位置，移动位置主要是为去重的，每次ijk都要移动到下一个不一样的，指针的移动这里要模拟do-while循环，这个非常关键

既然是要求和为0，可以想到进行排序O(nlogn)，从小到大。之后的思路感觉是一种O(n^2)的思路，设立ijk

- i代表：一个基准的数字，可以看做jk位置处的数字加起来要等于-i位置处的数字
- j，k代表：在i后边的数字序列中，j从头开始，k从尾开始，这样根据加了后的关系可以判断是j该往后移动还是k该往前移动

由于题目中要求答案中不可以包含重复的三元组，这里就是题目里代码实现起来很容易出错的部分了，i，j，k在每次遍历的时候都要走到下一个不一样的地方去，这里感觉有一种do-while循环的感觉

```python
# 在如下的操作下，j最终会停在不相等的位置上
j += 1
while j < k and nums[j] == nums[j-1]:
    j += 1
```

要分加起来等于0，小于0，大于0三个讨论，其中等于0的情况jk一起移动

有剪枝操作，在排序了后如果i位置处都大于0了，那么就可以结束了

解答如下
```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        
        """
        # O(n^3)，其中tuple这个操作用来去重用，tuple是可哈希化的，这种方法超时了
        rlist = []
        for i in range(len(nums)):
            for j in range(i+1, len(nums)):
                for k in range(j+1, len(nums)):
                    if nums[i] + nums[j] + nums[k] == 0:
                        tmplist = sorted([nums[i], nums[j], nums[k]])
                        rlist.append(tuple(tmplist))
        temp = list(set(rlist))
        return [[x[0], x[1], x[2]] for x in temp]
        """

        # 优化方法
        # O(nlogn)排序+ O(n^2)双指针，一前一后，跳过后
        rlist = []
        nums = sorted(nums)

        for i in range(len(nums)-2):
            # 学习题解中的剪枝操作
            if nums[i] > 0: 
                break
            if i > 0 and nums[i] == nums[i-1]: # 三元组不能重复，注意python的-1问题
                continue

            j = i + 1
            k = len(nums) - 1

            while j < k:
                if nums[i] + nums[j] + nums[k] == 0:
                    rlist.append([nums[i], nums[j], nums[k]])
                    # print(rlist)
                    # j移动到下一个不一样的
                    j += 1
                    while j < k and nums[j] == nums[j-1]:
                        j += 1

                    k -= 1
                    while k > j and nums[k] == nums[k+1]:
                        k -= 1
                    
                elif nums[i] + nums[j] + nums[k] > 0: # 太大了，k往左移动到一个不一样的
                    k -= 1
                    while k > j and nums[k] == nums[k+1]:
                        k -= 1
                else:
                    # 模拟do-while循环
                    j += 1
                    while j < k and nums[j] == nums[j-1]:
                        j += 1
    
        return rlist
```