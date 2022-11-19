---
title: 312.戳气球-python
date: 2021-12-16 23:42:30
tags:
    - 数组
    - 动态规划
    - HARD
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

312.戳气球（困难）

题目大意：

有```n```个气球，编号为```0```到```n - 1```，每个气球上都标有一个数字，这些数字存在数组```nums```中。

现在要求你戳破所有的气球。戳破第```i```个气球，你可以获得```nums[i - 1] * nums[i] * nums[i + 1]```枚硬币。 这里的```i - 1```和```i + 1```代表和```i```相邻的两个气球的序号。如果```i - 1```或```i + 1```超出了数组的边界，那么就当它是一个数字为```1```的气球。

求所能获得硬币的最大数量。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/burst-balloons/)

有```n```个气球，编号为```0```到```n - 1```，每个气球上都标有一个数字，这些数字存在数组```nums```中。

现在要求你戳破所有的气球。戳破第```i```个气球，你可以获得```nums[i - 1] * nums[i] * nums[i + 1]```枚硬币。 这里的```i - 1```和```i + 1```代表和```i```相邻的两个气球的序号。如果```i - 1```或```i + 1```超出了数组的边界，那么就当它是一个数字为```1```的气球。

求所能获得硬币的最大数量。

示例1：
```
输入：nums = [3,1,5,8]
输出：167
解释：
nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
coins =  3*1*5    +   3*5*8   +  1*3*8  + 1*8*1 = 167
```

示例2：
```
输入：nums = [1,5]
输出：10
```

提示：
- n == nums.length
- 1 <= n <= 500
- 0 <= nums[i] <= 100

# 分析和解答

这个题在没有见过之前几乎很难想到这个dp的思路，二维```dp[i][j]```数组代表的含义是，在开区间(i, j)之间所能获得的最大金币数。

**注意dp数组的初始化，避免深浅拷贝问题！**
```python
dp = [[0 for i in range(len(nums))] for j in range(len(nums))]  # 避免深浅拷贝问题
```

想到状态转移的递推关系，戳爆最后一个气球的时候，最后一个气球必然两边都是空的，也就是题目中说如果「如果```i - 1```或```i + 1```超出了数组的边界，那么就当它是一个数字为```1```的气球」，这个时候的状态，也就是```dp[0][len(nums)-1]```（注意这里的nums已经经过扩充，```nums.insert(0, 1)，nums.insert(len(nums), 1)```。

想在最后一个状态的状态转移方程，```dp[0][len(nums)-1] = dp[0][k] + dp[0]*dp[k]*dp[len(nums)-1] + dp[k][len(nums)-1]```，这里想法是要遍历k在整个区间上，找到一个最大的赋值给```dp[0][len(nums)-1]```。经过这个状态转移方程后，感觉可以慢慢想到是一种自底向上的动态规划策略，由于是开区间(i, j)，所以最小的窗口要从3开始，到len(nums)这个长度为止。然后用每个长度的窗口3、4 ... len(nums)在上边滑动。循环整体结构如下所示

```python
# 由于是自底向上的思想，要让这个开区间不断扩大，[2, len(nums))，注意是左闭右开区间
for window_size in range(2, len(nums)):
    # 在当前的窗口大小下，把整个数组过一遍，自底向上来不断修改dp中的值
    for i in range(0, len(nums)-window_size):
```

在每次的循环内部，需要计算```dp[i][i+window_size]```的值，这个就是上文中分析的开区间(i, j)之间能获得的最大金币数，遍历```for k in range(i+1, j)```，使用状态转移方程```dp[0][len(nums)-1] = dp[0][k] + dp[0]*dp[k]*dp[len(nums)-1] + dp[k][len(nums)-1]```，在不断的循环中窗口会扩大，也会自底向上。


完整解答如下：
```python
class Solution(object):
    def maxCoins(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """

        """
        自底向上动态规划感觉可以算非常经典的题目了？
        如果能想到dp数组的含义感觉还是可以理解的，但是感觉这个dp数组的含义实在不太好想
        
        假设 dp[i][j] 表示【【【开区间】】】 (i,j) 内你能拿到的最多金币
        想到这个递推的关系，删除【【【最后】】】一个气球k的时候，dp[i][k] + num[i]*num[j]*num[k] + dp[k][j]
        再加上这个题说如果i-1，i+1超出了数组的时候，就当他是一个数字为1的气球

        所以如果在开始和最后补充上两个1的话，在新的数组上开区间(0, len(nums)-1)所能拿到的最大值，就是最终的状态了
        这个状态在递推下，就等于最后一个删除的气球k，dp[i][k] + num[i]*num[j]*num[k] + dp[k][j]，有一种分而治之的思想

        由于是开区间，所以最小的长度为3才可以，也或许是先想出上边的递推公式后，再来看dp的思路，dp数组的含义是什么了，感觉还是得想状态转移

        最后来看的话，这个题是一个自底向上的动态规划问题，要首先找到小区间的，例如对于一个长度为3的开区间，永远就是当前窗口中间的那个数字
        
        解法的话按照答案的思路写了一遍，答案还是有很多内容需要注意到的
            - 包括区间不要让角标超出限制
            - 还有range_best中的ij参数是代表开区间，所以在遍历的时候for k in range(i+1, j)就可以这样的
        """
        def get_range_best(i, j):
            max_value = 0
            for k in range(i+1, j):
                tmp_value = dp[i][k] + nums[i] * nums[k] * nums[j] + dp[k][j]
                max_value = max(max_value, tmp_value)
            dp[i][j] = max_value


        nums.insert(0, 1)
        nums.insert(len(nums), 1)
        dp = [[0 for i in range(len(nums))] for j in range(len(nums))]

        # 由于是自底向上的思想，要让这个开区间不断扩大，[2, len(nums))，注意是左闭右开区间
        for window_size in range(2, len(nums)):
            # 在当前的窗口大小下，把整个数组过一遍，自底向上来不断修改dp中的值
            for i in range(0, len(nums)-window_size):
                get_range_best(i, i+window_size)  # 这个函数的作用是，找到在i, i+window_size这个开区间中，最大的那一个值，窗口大小是3（window_size=2时，就是那个值）
            
            # print(dp)
            # assert False
        
        return dp[0][len(nums)-1]
```