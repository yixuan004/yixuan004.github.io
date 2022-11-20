---
title: 62.不同路径-python
date: 2022-01-06 14:05:54
tags:
    - 数学
    - 动态规划
    - 组合数学
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

62.不同路径（中等）

题目大意：

一个机器人位于一个```m x n```网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/unique-paths/)

题目大意：

一个机器人位于一个```m x n```网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-06-14-14-44.png)
```
输入：m = 3, n = 7
输出：28
```

示例2：
```
输入：m = 3, n = 2
输出：3
解释：
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右
3. 向下 -> 向右 -> 向下
```

示例3：
```
输入：m = 7, n = 3
输出：28
```

示例4：
```
输入：m = 3, n = 3
输出：6
```

提示：
- ```1 <= m, n <= 100```
- 题目数据保证答案小于等于```2 * 109```

# 分析和解答

很明显的二维dp，向下走的所有格子和向右走的所有格子都只有一种走法，所以初始化为1

之后的状态转移方程很好想```dp[i][j] = dp[i-1][j] + dp[i][j-1]```

```python
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """

        dp = [[0 for i in range(n)] for i in range(m)]
        # 把第一行第一列进行初始化，第一行第一列都只有一种走法
        ## 第一行 
        for i in range(n):
            dp[0][i] = 1
        ## 第一列
        for i in range(m):
            dp[i][0] = 1

        # 开启新的dp状态转移
        for i in range(1, m):
            for j in range(1, n):
                # 新的mn位置处有几种到达走法
                dp[i][j] = dp[i][j-1] + dp[i-1][j]

        return dp[m-1][n-1]
```