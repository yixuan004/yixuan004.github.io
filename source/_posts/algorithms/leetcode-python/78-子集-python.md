---
title: 78.子集-python
date: 2022-02-21 18:51:36
tags:
    - 位运算
    - 数组
    - 回溯
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

78.子集（中等）

题目大意：

给你一个整数数组 ```nums``` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/subsets/)

给你一个整数数组 ```nums``` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

示例1：
```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

示例2：
```
输入：nums = [0]
输出：[[],[0]]
```

提示：
- ```1 <= nums.length <= 10```
- ```-10 <= nums[i] <= 10```
- ```nums``` 中的所有元素 **互不相同**


# 分析和解答

## 解法一：2^n遍历法

在某次周赛的时候积累到了这个思路，可以通过位运算的方法把0到n的二进制每个位的表示都提取出来，这样就可以实现每个位置选择/不选择的2^n遍历法。

```python
class Solution(object):
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """

        # 某次周赛积累到的2^n这种的遍历思路，可以实现暴力非递归
        def get_bits(num):
            l = []
            while num > 0:
                l.append(num&1)
                num >>= 1
            return l

        ans = []
        for i in range(2**len(nums)):
            l = get_bits(i)
            tmp = []
            for j in range(len(l)):
                if l[j] == 1:
                    tmp.append(nums[j])
            ans.append(tmp)
        return ans
```


## 解法二：dfs回溯法

dfs的方法在本质上也是针对每个位置的选择，不选择。所以每次carry到递归下一个轮次状态就是位置i，**针对位置i的选择和不选择两条dfs分支**。

另外在这个题里发现dfs的时候需要deepcopy复制一份，列表在dfs过程中不能指向相同的地址，否则一个改动了就全部改动了。

```python
class Solution(object):
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        # 递归思路，这个题涉及到了python的深浅拷贝问题，在dfs向下一层的时候本质上是复制了状态，所以要拷贝
        res = []
        def dfs(i, tmp_list):
            """
            :type i: int，代表当前到了dfs的第几层了
            : type tmp_list: List[int]，代表当前带着哪些下到下一层了！！！！
            """
            if i == len(nums):  # 这类的递归一般用长度作为出口
                res.append(tmp_list)
                return

            # 不取的dfs分支
            dfs(i+1, copy.deepcopy(tmp_list))  # 这里要传拷贝，否则会搞到一个分支上！！！
            # 取的dfs分支
            tmp_list.append(nums[i])
            dfs(i+1, copy.deepcopy(tmp_list))  # 这里要传拷贝
            
        dfs(0, [])
        return res
```