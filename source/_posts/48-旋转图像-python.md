---
title: 48.旋转图像-python
date: 2022-01-05 21:41:05
tags:
    - 数组
    - 数学
    - 矩阵
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

48.旋转图像（中等）

题目大意：

给定一个```n × n```的二维矩阵```matrix```表示一个图像。请你将图像顺时针旋转```90```度。

你必须在**原地**旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要**使用另一个矩阵来旋转图像。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/rotate-image/)

题目大意：

给定一个```n × n```的二维矩阵```matrix```表示一个图像。请你将图像顺时针旋转```90```度。

你必须在**原地**旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要**使用另一个矩阵来旋转图像。

示例1：
![](/images/2022-01-05-21-46-28.png)
```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

示例2：
![](/images/2022-01-05-21-46-42.png)
```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

示例3：
```
输入：matrix = [[1]]
输出：[[1]]
```

示例4：
```
输入：matrix = [[1,2],[3,4]]
输出：[[3,1],[4,2]]
```

提示：
- ```matrix.length == n```
- ```matrix[i].length == n```
- ```1 <= n <= 20```
- ```-1000 <= matrix[i][j] <= 1000```


# 分析和解答

直接硬转实在是太考验人了，所以现在是两种方法，这样算起来就变简单非常多了

- 先水平翻转，再沿副对角线翻转
- 先上下翻转，再沿主对角线翻转


```python
class Solution(object):
    def rotate(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: None Do not return anything, modify matrix in-place instead.
        """

        # 首先进行左右翻转，然后按照副对角线进行翻转，在torch里是triu相关的操作，哈哈
        n = len(matrix)  # 一个 nxn 的矩阵
        ## 执行左右翻转
        for i in range(n):
            for j in range(n//2):
                # 执行翻转
                temp = matrix[i][j]
                matrix[i][j] = matrix[i][n-1-j]
                matrix[i][n-1-j] = temp
        # print(matrix)
        ## 按照副对角线进行翻转，事后复盘来看的话，按照副对角线翻转有些复杂了
        for i in range(n):
            for j in range(n-1-i):
                # 执行翻转
                temp = matrix[i][j]
                matrix[i][j] = matrix[n-1-j][n-1-i]
                matrix[n-1-j][n-1-i] = temp
                
                # print(i, j)
        
        # print(matrix)
```