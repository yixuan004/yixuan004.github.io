---
title: 200.岛屿数量-python
date: 2021-12-17 09:51:06
tags:
    - 深度优先搜索
    - 广度优先搜索
    - 并查集
    - 数组
    - 矩阵
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

200.岛屿数量（中等）

题目大意：

给你一个由```'1'```（陆地）和```'0'```（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/number-of-islands/)

给你一个由```'1'```（陆地）和```'0'```（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

示例1：
```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

示例2：
```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

提示：
- ```m == grid.length```
- ```n == grid[i].length```
- ```1 <= m, n <= 300```
- ```grid[i][j] 的值为 '0' 或 '1'```

# 分析和解答

poj2386 Couting Lake的模板[题目链接](http://poj.org/problem?id=2386)

走到每个位置上dfs，dfs不需要回溯所以很简单，就是把grid的所有状态修改了，这个题做过比较多次了比较熟悉了

```python
class Solution(object):
    def numIslands(self, grid):
        """
        :type grid: List[List[str]]
        :rtype: int
        """
        def dfs(x, y):
            grid[x][y] = "0"
            dx = [1, 0, -1, 0]
            dy = [0, 1, 0, -1]
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if 0 <= nx < len(grid) and 0 <= ny < len(grid[0]) and grid[nx][ny] == "1":
                    dfs(nx, ny)
        cnt = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] == "1":
                    cnt += 1
                    dfs(i, j)
        return cnt
```