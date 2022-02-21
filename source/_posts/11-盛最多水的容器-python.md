---
title: 11.盛最多水的容器-python
date: 2021-12-15 18:48:09
tags:
    - 贪心
    - 数组
    - 双指针
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

11.盛最多水的容器（中等）

题目大意：

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/container-with-most-water/)

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器。


示例1：
![](/images/2021-12-15-18-56-13.png)
```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```


示例2：
```
输入：height = [1,1]
输出：1
```

示例3：
```
输入：height = [4,3,2,1,4]
输出：16
```

示例4：
```
输入：height = [1,2,1]
输出：2
```

提示：
- n == height.length
- 2 <= n <= 105
- 0 <= height[i] <= 104


# 分析与解答

O(n^2)的方法很会超时

对上述方法采用双指针进行优化，整体性的思路如下：

在整体上一定存在一个长板和一个短板，初始化下的面积为 短板高度 * (j-i) ```min(a[i], a[j]) * (j-i)```

由于水桶的容量是收到短板影响的，所以怎么“往内”移动长板都是没用的，要么是还受到现在的短板作用，要么是只会找到一个比现在短板更短的短板，由于是往内移动的，所以只会进一步缩小

所以要把短板往内移动，每次移动的时候记录当前的值并进行比较，注意在移动的过程中要更新短板（贪心的思想应该隐含在这里了）

```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        # O(n^2) time limit了
        """
        max_water = 0
        for i in range(len(height)):
            for j in range(i, len(height)):
                max_water = max(max_water, (j-i)*min(height[i], height[j]))
        return max_water
        """
        
        """
        对上述方法采用双指针进行优化，整体性的思路如下：
            在整体上一定存在一个长板和一个短板，初始化下的面积为 短板高度 * (j-i)
            由于水桶的容量是收到短板影响的，所以怎么“往内”移动长板都是没用的，要么是还受到现在的短板作用，要么是只会找到一个比现在短板更短的短板，由于是往内移动的，所以只会进一步缩小
            所以要把短板往内移动，每次移动的时候记录当前的值并进行比较，注意在移动的过程中要更新短板
        """
        if len(height) < 2:
            return 0
        
        i = 0
        j = len(height) - 1
        max_water = (j-i) * min(height[i], height[j])
        
        while i < j:
            if height[i] <= height[j]:  # 等号随便给一边就可以了
                while i < j and height[i] <= height[j]:
                    max_water = max(max_water, (j-i) * min(height[i], height[j]))
                    i += 1
            else:
                while j > i and height[j] <= height[i]:
                    max_water = max(max_water, (j-i) * min(height[i], height[j]))
                    j -= 1
        
        return max_water
```