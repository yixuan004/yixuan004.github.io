---
title: 64.最小路径和-python
date: 2022-01-06 14:25:29
tags:
    - 数组
    - 动态规划
    - 矩阵
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

64.最小路径和（中等）

题目大意：

给定一个包含非负整数的```m x n```网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/minimum-path-sum/)

题目大意：

给定一个包含非负整数的```m x n```网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。


示例1：
![](/images/2022-01-06-14-30-08.png)
```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

示例2：
```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

提示：
- m == grid.length
- n == grid[i].length
- 1 <= m, n <= 200
- 0 <= grid[i][j] <= 100


# 分析和解答

也是一眼就能看出来是二维dp的题目，往下走和往右都是只有一种不断累加的走法

之后的状态转移方程```dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]```

```python
class Solution(object):
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """

        # 一眼dp，然后想想dp的思路应该也挺简单的，做一个min应该就行吧
        m = len(grid)
        n = len(grid[0])
        dp = [[0 for i in range(n)] for j in range(m)]
        
        # 初始化dp数组的第一行，第一列
        ## 初始化dp数组的第一行
        now = 0
        for i in range(n):
            now += grid[0][i]
            dp[0][i] = now
        ## 初始化dp数组的第一列
        now = 0
        for i in range(m):
            now += grid[i][0]
            dp[i][0] = now
        # print(dp)

        ##
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
        
        return dp[m-1][n-1]
```