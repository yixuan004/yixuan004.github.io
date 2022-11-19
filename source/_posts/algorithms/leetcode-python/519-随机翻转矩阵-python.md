---
title: 519.随机翻转矩阵-python
date: 2021-12-03 10:45:23
tags:
    - 水塘抽样
    - 哈希表
    - 数学
    - 随机化
    - MEDIUM
    - TODO优化
categories:
	- LeetCode-python
---

519.随机翻转矩阵（中等）

题目大意：给你一个 m x n 的二元矩阵 matrix ，且所有值被初始化为 0 。请你设计一个算法，随机选取一个满足 matrix[i][j] == 0 的下标 (i, j) ，并将它的值变为 1 。所有满足 matrix[i][j] == 0 的下标 (i, j) 被选取的概率应当均等。

尽量最少调用内置的随机函数，并且优化时间和空间复杂度。

实现 Solution 类：
- Solution(int m, int n) 使用二元矩阵的大小 m 和 n 初始化该对象
- int[] flip() 返回一个满足 matrix[i][j] == 0 的随机下标 [i, j] ，并将其对应格子中的值变为 1
- void reset() 将矩阵中所有的值重置为 0

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/random-flip-matrix/)

给你一个 m x n 的二元矩阵 matrix ，且所有值被初始化为 0 。请你设计一个算法，随机选取一个满足 matrix[i][j] == 0 的下标 (i, j) ，并将它的值变为 1 。所有满足 matrix[i][j] == 0 的下标 (i, j) 被选取的概率应当均等。

尽量最少调用内置的随机函数，并且优化时间和空间复杂度。

实现 Solution 类：
- Solution(int m, int n) 使用二元矩阵的大小 m 和 n 初始化该对象
- int[] flip() 返回一个满足 matrix[i][j] == 0 的随机下标 [i, j] ，并将其对应格子中的值变为 1
- void reset() 将矩阵中所有的值重置为 0

示例：
```
输入
["Solution", "flip", "flip", "flip", "reset", "flip"]
[[3, 1], [], [], [], [], []]
输出
[null, [1, 0], [2, 0], [0, 0], null, [2, 0]]

解释
Solution solution = new Solution(3, 1);
solution.flip();  // 返回 [1, 0]，此时返回 [0,0]、[1,0] 和 [2,0] 的概率应当相同
solution.flip();  // 返回 [2, 0]，因为 [1,0] 已经返回过了，此时返回 [2,0] 和 [0,0] 的概率应当相同
solution.flip();  // 返回 [0, 0]，根据前面已经返回过的下标，此时只能返回 [0,0]
solution.reset(); // 所有值都重置为 0 ，并可以再次选择下标返回
solution.flip();  // 返回 [2, 0]，此时返回 [0,0]、[1,0] 和 [2,0] 的概率应当相同
```

提示：
- 1 <= m, n <= 104
- 每次调用flip 时，矩阵中至少存在一个值为 0 的格子。
- 最多调用 1000 次 flip 和 reset 方法。

# 分析和解答

这个题如果未来会被问到的话，首先需要自己熟悉一下手写基础框架
```python
class Solution(object):
    def __init__(self, m, n):
        self.m = m
        self.n = n
        # do something

    def filp(self):
        # do something
        pass

    def reset(self):
        # do something
        pass
```

目前使用一种非常简单的方法就解决了，每次随机一个[0, m-1]，随机一个[0, n-1]（使用random.randint(a, b)，注意这里a，b都是闭区间的），用一个dict判断d[(rand_m, rand_n)]是否有，如果有就一直重新随机，直到不存在，不存在的情况下加入dict。清空则就是清空词典。但这个运气最差得情况下会调用很多次随机函数，所以实际上不符合题意的，只是比较简单实现，暂时不属于hot100题就未来补充了。
```python
class Solution(object):

    def __init__(self, m, n):
        """
        :type m: int
        :type n: int
        """
        self.m = m
        self.n = n
        self.temp_dict = {}

    def flip(self):
        """
        :rtype: List[int]
        """
        # 把self的值取过来
        m = self.m
        n = self.n
        
        m_temp = random.randint(0, m-1)
        n_temp = random.randint(0, n-1)

        while self.temp_dict.get((m_temp, n_temp)) is not None:
            m_temp = random.randint(0, m-1)
            n_temp = random.randint(0, n-1)
        self.temp_dict[(m_temp, n_temp)] = 1
        return [m_temp, n_temp]

    def reset(self):
        """
        :rtype: None
        """
        self.temp_dict = {}

# Your Solution object will be instantiated and called as such:
# obj = Solution(m, n)
# param_1 = obj.flip()
# obj.reset()
```


# 优化解法（减少随机函数的调用次数）待补充